.. AIS Connector Library documentation master file, created by
   sphinx-quickstart on Wed Jul 31 09:53:10 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Release Note
=================================================

Latest version :code:`1.0.0`

1. Adding permision in manifest

	::

		<uses-permission android:name="android.permission.BLUETOOTH"/>
		<uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>
		<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>


	You can see this changes on the :ref:`Implementation <Implementation>`  page.

2. Update :code:`BluetoothLEHelper` initialization with sample code for configure fine location permission

	You can see this changes on the :ref:`Implementation <Implementation>`  page.

3. Adding :code:`DataManager.init(getApplicationContext());` for read the return message

	You can see this changes on the :ref:`Implementation <Implementation>`  page and :ref:`SOS Function <sos_function>` page.

4. Adding tracking functionality  

	You can see this changes on the :ref:`Tracking Function <tracking_function>` page