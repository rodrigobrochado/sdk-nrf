.. _bt_fast_pair_provision_script:

Fast Pair provision script
##########################

.. contents::
   :local:
   :depth: 2

This Python script generates a hexadecimal file required for the :ref:`Google Fast Pair integration in the nRF Connect SDK <ug_bt_fast_pair>`.

.. note::
   When you :ref:`use Google Fast Pair with the nRF Connect SDK <ug_bt_fast_pair>`, the script is automatically called by the build system.
   There is no need to generate and program the hexadecimal file manually.
   See :ref:`ug_bt_fast_pair_provisioning` for details.

Overview
********

The generated hexadecimal file contains the following data obtained during the Fast Pair Provider model registration:

* Model ID
* Anti-Spoofing Private Key

See `Fast Pair Model Registration`_ in the Google Fast Pair Service documentation for details.

Requirements
************

The script source files are located in the :file:`scripts/nrf_provision/fast_pair` directory.

The script uses the `IntelHex`_ Python library to generate the hexadecimal file.

To install the script's requirements, run the following command in its directory:

.. code-block:: console

   python3 -m pip install -r requirements.txt

Using the script
****************

Run the script commands in the :file:`scripts/nrf_provision/fast_pair` directory to start using the script.
To list the script arguments, run the following command:

.. code-block:: console

   python3 fp_provision_cli.py -h

The following help information describes the available script arguments:

.. code-block:: console

   Fast Pair Provisioning Tool

   optional arguments:
     -h, --help            show this help message and exit
     -o OUT_FILE, --out_file OUT_FILE
                           Name of the output file
     -a ADDRESS, --address ADDRESS
                           Address of provisioning partition start (in hex)
     -m MODEL_ID, --model_id MODEL_ID
                           Model ID (in format 0xXXXXXX)
     -k ANTI_SPOOFING_KEY, --anti_spoofing_key ANTI_SPOOFING_KEY
                           Anti Spoofing Key (base64 encoded)

The following commands show an example of a script call that uses short names of arguments:

.. code-block:: console

    python3 fp_provision_cli.py -o=provision.hex -a=0x50000 -m="0xFFFFFF" -k="AbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbA="

The following commands show an example of a script call that uses full names of arguments:

.. code-block:: console

    python3 fp_provision_cli.py --out_file=provision.hex --address=0x50000 --model_id="0xFFFFFF" --anti_spoofing_key="AbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbA="

Make sure to replace the following parameter values:

* ``0xFFFFFF`` - Add your Model ID.
* ``AbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbAbA=`` - Add your Anti Spoofing Key.
* ``0x50000`` - Add the address of the ``bt_fast_pair`` partition defined by the :ref:`partition_manager`.

Implementation details
**********************

The script converts the Model ID and Anti-Spoofing Private Key to bytes and places them under the predefined offsets in the generated hexadecimal file.
The ID and the key are required by the Google Fast Pair Service and are provided as command line arguments (see `Using the script`_).
The magic data with predefined value is placed right before and right after the mentioned provisioning data.

The generated data must be placed on a dedicated ``bt_fast_pair`` partition defined by the :ref:`partition_manager`.
The :ref:`bt_fast_pair_readme` knows both the offset sizes and lengths of the individual data fields in the provisioning data.
The service accesses the data by reading flash content.

Dependencies
************

The script uses the `IntelHex`_ Python library.
