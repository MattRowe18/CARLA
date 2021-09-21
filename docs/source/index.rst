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

Then launch the CARLA server:

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
much slower than in the image above. However, you can alter the speed using arguments for the python script.

There are 3 arguments to the dynamic_weather.py script:

.. list-table:: Title
   :widths: 20, 80
   :header-rows: 0

   * - --host
     - set the CARLA host IP (by default 127.0.0.1 or 'localhost')
   * - --port
     - set the port to find the SERVER server (by default port 2000)
   * - --speed
     - sets the speed of the weather changes by multiplier (default is 1.0)


.. image :: dynamic_weather_render.gif

.. code-block:: python

   class Sun(object):
      def __init__(self, azimuth, altitude):
         self.azimuth = azimuth
         self.altitude = altitude
         self._t = 0.0

      def tick(self, delta_seconds):
         self._t += 0.008 * delta_seconds
         self._t %= 2.0 * math.pi
         self.azimuth += 0.25 * delta_seconds
         self.azimuth %= 360.0
         self.altitude = (70 * math.sin(self._t)) - 20

      def __str__(self):
         return 'Sun(alt: %.2f, azm: %.2f)' % (self.altitude, self.azimuth)

Check out the :doc:`usage` section for further information, including
how to :ref:`installation` the project.



.. note::

   The rendered view changes much slower than this.

Contents
--------

.. toctree::

   usage
   api
