.. AIS Connector Library documentation master file, created by
   sphinx-quickstart on Wed Jul 31 09:53:10 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. _Implementation:

Implementation
=================================================

Follow these steps to implement AIS Connector Library in your android app project.

**Note: Use Minimum API level 21**

**Add module to android project**

1. Add depedencies to your Android project

	::

		implementation 'com.github.karimfatkhul:bleaislibrary:1.0.1'

2. Then add the token to your gradle.properties: 
	
	::

		authToken=jp_68gckhjthli3d7i84515it52dk

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

4. BLE permissions

	::

		<uses-permission android:name="android.permission.BLUETOOTH"/>
		<uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>
		<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

5. Initialize :code:`BluetoothLEHelper`

	You can use :code:`BluetoothLEHelper ble = new BluetoothLEHelper(getActivity());;` for enabling blueetooth service.

	This snippet below is sample of the initialize :code:`BluetoothLEHelper` and :code:`fine location configure permission`

	::

		 private static final int ACCESS_FINE_LOCATION_REQUEST = 2;
		 BluetoothLEHelper ble;

		 protected void onCreate(Bundle savedInstanceState) {
		 	if(hasPermissions()) {
		             initBluetoothHandler();
		        }
		 	} 
		 	
		 private void initBluetoothHandler()
		     {
		         DataManager.init(getApplicationContext());
		         //Enabling bluetooth service
		         ble = new BluetoothLEHelper(this);

		     }

		 private boolean hasPermissions() {
		        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
		            if (getApplicationContext().checkSelfPermission(Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
		                requestPermissions(new String[] { Manifest.permission.ACCESS_FINE_LOCATION }, ACCESS_FINE_LOCATION_REQUEST);
		                return false;
		            }
		        }
		        return true;
		    }

		  @Override
		  public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
		      switch (requestCode) {
		          case ACCESS_FINE_LOCATION_REQUEST:
		              if(grantResults.length > 0) {
		                  if(grantResults[0] == PackageManager.PERMISSION_GRANTED) {
		                      initBluetoothHandler();
		                  }
		              }
		              break;
		          default:
		              super.onRequestPermissionsResult(requestCode, permissions, grantResults);
		              break;
		      }
		  }


6. Connecting to specify AIS Devices

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

	There are 2 return message type of the method above. Connected and Disconnected

	This snippet below is code to check the return message of :code:`ble.connectedDevice(true, BLEAddress)` method.

	::

		DataManager.getInstance().getString(KeyConstants.KEY_STATUS_BLUETOOTH, KeyConstants.KEY_STRING_DEFAULT)

		
7. Disconnecting from AIS Devices

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