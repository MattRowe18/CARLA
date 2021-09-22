CARLA Dynamic Weather tutorial
===================================

Welcome to the CARLA Dynamic Weather tutorial. In this tutorial we will explore the ``dynamic_weather.py``
script supplied with the Python API examples as part of the CARLA simulator package.

**CARLA** is an advanced simulator for the generation of data for training autonomous
driving solutions. The simulator provides a plethora of options to mimic real world 
phenomena that could affect the performance of autonomous vehicles like changing lighting
conditions, rain, fog, clouds and night.

The Dynamic Weather script sets up a simulated world with changeable weather, the script 
sets up a moving lightsource from the sun that dips below the horizon to create a nights
scene and periodically introduces inclement weather like rain and fog. 

Before you try to run the ``dynamic_weather.py`` script, ensure you have the necessary prerequisites:

- A Windows or Linux computer with a powerful GPU
- A working installation of the CARLA simulator
- A working installation of Python 2.7 or 3.X on Linux and Python 3.X on Windows

Preparation
############

Install the `CARLA simulator <https://carla.readthedocs.io/en/latest/start_quickstart/#carla-installation>`_

Install the Python CARLA package using pip:

.. code-block:: console 

   pip install carla

.. code-block:: console 
   
   pip3 install carla

Then launch the CARLA server with default configuration:

In Windows

.. code-block:: console 

   \PATH\TO\CARLA\ROOT\CarlaUE4.exe

In Linux

.. code-block:: console 
   
   ./PATH/TO/CARLA/ROOT/CarlaUE4.sh

This will launch CARLA in server mode, by default it should be listening to port 2000. In Windows, depending 
on firewall settings, you may have to create rules to allow connections on port 2000. You will see a new window
open with the title **CarlaUE4**, this is the rendering portal. The window may be black for a few seconds, 
but once the CARLA simulator is running it will show a view of the default map Town01.

.. image :: default_render.PNG

You can navigate through the map by flying the camera using the WASD keys to move the camera
and dragging the mouse to rotate the camera. Take some time to fly through the map and explore the town.

Running the script
############

Now that CARLA is running, you can make use of the ``dynamic_weather.py`` script. Locate the PythonAPI/examples folder
inside the CARLA root directory. In there are a number of example scripts to experiment with
CARLA through the Python API. Run the script from the command line:

.. code-block:: console

   python3 dynamic_weather.py

You will now see the weather change from the default scene. Keep watching the screen for a few minutes, you
will observe changes of light from night to day, changes in cloud cover, fog and rain. By default the weather changes
much slower than in the animated image below. However, you can alter the speed of the weather changes using arguments for the Python script.

.. image :: dynamic_weather_render.gif

Command line arguments
######################

There are 3 command line arguments for the ``dynamic_weather.py`` script:

.. list-table::
   :widths: 20 80
   :header-rows: 0

   * - ``--host``
     - set the CARLA host IP (by default 127.0.0.1 or 'localhost')
   * - ``--port``
     - set the port to find the CARLA server (by default port 2000)
   * - ``--speed``
     - changes the speed of the weather changes wih multiplying factor (by default 1.0)

How the script works
#################

Now let's take a look inside the script to understand how to manipulate the simulation using Python commands. Open the
``dynamic_weather.py`` file in a code editor.

The script first declares some utility classes, to help set up and manipulate parameters for the sun, storms and the weather as a whole. Inside these classes you will find code that sets initial parameters within the ``__init__`` function of each class.
Within each class is a ``tick()`` function, this function is called when the simulation is advanced and the various weather parameters governing the angular position of the sun, the amount of rain, fog and clouds are modified by a set amount. The ``Weather`` class calls both the
``Sun`` and ``Storm`` classes. 

The ``dynamic_weather.py`` script sets up a connection between the Python process and the CARLA server at line 132, after parsing the command line arguments:

.. code-block:: python

   client = carla.Client(args.host, args.port)
   client.set_timeout(2.0)
   world = client.get_world()

   weather = Weather(world.get_weather())

The CARLA server should be running by default on port 2000, if you are having trouble running the script, this would be the first thing to check. The ``world`` object enables the Python process to request alterations to the simulation running in the CARLA server instance and the weather
object collects and modifies the simulation parameters associated with the weather. 

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
can compute them and will emit a signal to notify any clients that it has advanced in simulation time. The ``wait_for_tick()`` function notifies the Python process that 
a signal has been received from the CARLA server to continue and update the weather parameters. This is in contrast to the `synchronous mode <https://carla.readthedocs.io/en/latest/adv_synchrony_timestep/>`_, whereby a client can control the
progression of time within the simulation using the ``tick()`` function.

Manipulating the simulator directly from the Python command line
############

Next let's explore the API by manipulating the simulator directly from the Python command prompt. Open a new terminal and start Python:

.. code-block:: console

   >>> import carla 
   >>> client = carla.Client('localhost',2000)
   >>> world = client.get_world()

Now we've given the Python process a connection to the CARLA server accessible through the world variable. We can access the current state of the weather and modify it. Keep the CARLA viewport visible and keep an eye out for the changes you make:

.. code-block:: console

   >>> weather = world.get_weather()
   >>> weather.fog_density = 30.0
   >>> world.set_weather(weather)

Use ``list(filter(lambda x: x.find('__'), dir(weather)))`` to list the attributes of the weather object. The lowercase list elements are the parameters that can be edited, try changing some other parameters like in the code snippets above and observe the effects. You will also see some CamelCase attributes. These are 
weather parameter presets for rapidly setting up common weather scenarios. Pick some and try them out:

.. code-block:: console

   >>> world.set_weather(weather.SoftRainSunset)
   >>> world.set_weather(weather.WetCloudyNight)
   >>> world.set_weather(weather.MidRainSunset)

.. image :: command_line.gif

We hope you enjoyed the tutorial and understand now how to manipulate the weather in the CARLA simulator using scripts or directly from the Python console. See the `Python API <https://carla.readthedocs.io/en/latest/python_api/>`_ reference for more details about the functionality of the API.
