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
   
   pip3 install carla

Check out the :doc:`usage` section for further information, including
how to :ref:`installation` the project.

.. image :: dynamic_weather_render.gif

.. note::

   This project is under active development.

Contents
--------

.. toctree::

   usage
   api
