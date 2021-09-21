Welcome to the CARLA Dynamic Weather tutorial!
===================================

**CARLA** is an advanced simulator for the generation of data for training autonomous
driving solutions. The simulator provides a plethora of options to mimic real world 
phenomena that could affect the performance of autonomous vehicles like changing lighting
conditions, rain, fog, clouds and night.

The Dynamic Weather script sets up a simulated world with changeable weather, the script 
sets up a moving lightsource from the sun that dips below the horizon to create a nights
scene and periodically indroduces inclement weather like rain and fog. 

Before you try to run the dynamic_weather.py script, ensure you have the necessary prerequisites:

- A Windows or Ubuntu 18.04+ computer
- A working installation of the CARLA
- A working installation of Python 3.X

Install the `CARLA simulator <https://carla.readthedocs.io/en/latest/start_quickstart/#carla-installation>`_

Install the Python CARLA package using pip:

.. code-block:: console 

   pip install carla

.. code-block:: console 
   
   pip3 install carla #Python 3.X

Then launch the CARLA server with default configuration:

In Windows

.. code-block:: console 

   \PATH\TO\CARLA\ROOT\CarlaUE4.exe

In Ubuntu

.. code-block:: console 
   
   ./PATH/TO/CARLA/ROOT/CarlaUE4.sh

This will launch CARLA in server mode, by default it should be listening to port 2000. In Windows, depending 
on firewall settings, you may have to create rules to allow connections on port 2000. You will see a new window
open with title **CarlaUE4**, this is the rendering portal. The window may contain only black for a few seconds, 
but once the CARLA simulator is running, it will show a view of the default map Town01.

.. image :: default_render.PNG

You can navigate through the map by flying the camera using the WASD keys to move the camera
and dragging the mouse to rotate the camera. Take some time to fly through the map and explore the town.

Now that CARLA is running, you can make use of the Dynamic Weather script. Locate the PythonAPI/examples folder
inside the CARLA root directory. In here are a number of example scripts to experiment with
CARLA through the Python API. Run the script from the command line:

.. code-block:: console

   python3 dynamic_weather.py

You will now see the weather change from the default scene. Keep watching the screen for a few minutes, you
will observe changes of light, from night to day, changes in cloud cover, fog and rain. By default, the weather changes
much slower than in the image below. However, you can alter the speed of the weather changes using arguments for the python script.

.. image :: dynamic_weather_render.gif

There are 3 command line arguments for the dynamic_weather.py script:

.. list-table::
   :widths: 20 80
   :header-rows: 0

   * - ``--host``
     - set the CARLA host IP (by default 127.0.0.1 or 'localhost')
   * - ``--port``
     - set the port to find the CARLA server (by default port 2000)
   * - ``--speed``
     - changes the speed of the weather changes wih multiplying factor (default is 1.0)

Now let's take a look inside the script to understand how to manipulate the simulation. Open the
dynamic_weather.py file in a code editor.

The script first declares some utility classes, to help set up and manipulate parameters for the sun, storms and the weather as a whole. Inside these classes, you will find code that sets initial parameters within the ``__init__`` function of each class.
Within each class is a ``tick()`` function, this function is called when the simulation is advanced and the various weather parameters governing the angular position of the sun, the amount of rain, fog and clouds are modified by a set amount. The ``Weather`` class calls both the
``Sun`` and ``Storm`` classes. 

The dynamic_weather script sets up a connection to CARLA at line 132, after parsing the command line arguments:

.. code-block:: python

   client = carla.Client(args.host, args.port)
   client.set_timeout(2.0)
   world = client.get_world()

   weather = Weather(world.get_weather())

The ``world`` object enables the python script to request alterations to the simulation running in the CARLA server instance and the weather
object enables the python script to request changes to the weather. 

The following indefinite loop then continuously modifies the weather parameters as the simulation runs. 

.. code-block:: python

       while True:
        timestamp = world.wait_for_tick(seconds=30.0).timestamp
        elapsed_time += timestamp.delta_seconds
        if elapsed_time > update_freq:
            weather.tick(speed_factor * elapsed_time)
            world.set_weather(weather.weather)
            sys.stdout.write('\r' + str(weather) + 12 * ' ' + str(elapsed_time))
            sys.stdout.flush()
            elapsed_time = 0.0

In this case, the CARLA simulator is running in asynchronous mode (the default mode). This means that the CARLA simulator will calculate and render frames as fast as it
can compute them and will emit a signal to notify a client that it has advanced in simulation time. The ``wait_for_tick`` function enables python to wait until 
a signal is received from the CARLA server to continue and update the weather parameters. This is in contrast to the synchronous mode, whereby a client can control the
progression of time within the simulation using the ``tick()`` function. See the `Python API <https://carla.readthedocs.io/en/latest/python_api/>`_ reference for details.
