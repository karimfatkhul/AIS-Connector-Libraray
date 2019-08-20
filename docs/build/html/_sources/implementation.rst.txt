.. AIS Connector Library documentation master file, created by
   sphinx-quickstart on Wed Jul 31 09:53:10 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Implementation
=================================================

Follow these steps to implement AIS Connector Library in your android app project.

**Add module to android project**

1. Add depedencies to your Android project

	::

		implementation 'com.github.nugdoel:bleaislibrary:0.0.1'


2. Then add the token to your gradle.properties: 
	
	::

		authToken=jp_1bqr3p2jn9978mh2ngp8312jqc

3. Add the JitPack repository to your build file

	::

		allprojects {
		    repositories {
		        google()
		        jcenter()
		        maven {
		            url "https://jitpack.io"
		            credentials { username authToken }
		        }
		        
		    }
		}

4. Initialize :code:`BLEadapter`

	::

		 BluetoothLEHelper ble;
		 //Enabling bluetooth service
		 ble = new BluetoothLEHelper(getActivity());
		 
		 DataManager.init(getApplicationContext());

5. Connecting to specify AIS Devices

	Use :code:`ble.connectedDevice(true, BLEAddress)` method to connected to the device.

	This snippet below is sample usage of the :code:`connectedDevice()` method

	::

		btnStart = (Button) view.findViewById(R.id.start_scanning_button);

		        btnStart.setOnClickListener(new View.OnClickListener()
		        {
		            @Override
		            public void onClick(View v)
		            {
		                 String addressBLE = "5C:F8:21:96:73:DB";
		                 ble.connectedDevice(true, addressBLE);

		            }
		        });

6. Disconnecting from AIS Devices

	Use :code:`ble.disconnectedDevice()` method to disconnecting to the device.

	This snippet below is sample usage of the :code:`disconnectedDevice()` method

	::

		btnStop = (Button) view.findViewById(R.id.start_stop_button);

		        btnStop.setOnClickListener(new View.OnClickListener()
		        {
		            @Override
		            public void onClick(View v)
		            {
		                 ble.disconnectedDevice();

		            }
		        });