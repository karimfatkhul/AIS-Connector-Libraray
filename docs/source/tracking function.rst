.. AIS Connector Library documentation master file, created by
   sphinx-quickstart on Wed Jul 31 09:53:10 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Tracking Function
=================================================


Use :code:`sendRequestTracking()` method to send tracking request to the AIS Device.

There are 2 return message type of :code:`sendRequestTracking()` method.

**1. Locked**
Locked indicate that the ship has locked the location. If the return message is locked we get 3 data, these are mmsi, shipname and lat lon.

**2. Unlocked** 
Unlocked indicate that the ship hasn't locked the location, we just get 2 data these are mmsi and shipname.

This snippet bellow is code to check return message of :code:`sendRequestTracking()` method.

	::

		DataManager.getInstance().getString(KeyConstants.KEY_STATUS_TRACKING, KeyConstants.KEY_STRING_DEFAULT)


We can use the code bellow to read the return data of :code:`sendRequestTracking()` method.

	::

		//get ship name
		DataManager.getInstance().getString(KeyConstants.KEY_SHIP_NAME, KeyConstants.KEY_STRING_DEFAULT)

		//get mmsi
		DataManager.getInstance().getString(KeyConstants.KEY_MMSI, KeyConstants.KEY_STRING_DEFAULT)

		//get latitude
		DataManager.getInstance().getString(KeyConstants.KEY_LATITUDE, KeyConstants.KEY_STRING_DEFAULT)

		//get longtitude
		DataManager.getInstance().getString(KeyConstants.KEY_LONGTITUDE, KeyConstants.KEY_STRING_DEFAULT)		

