.. AIS Connector Library documentation master file, created by
   sphinx-quickstart on Wed Jul 31 09:53:10 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Implementation
=================================================

Follow these steps to implement AIS Connector Library in your android app project.

**Add module to android project**

1. Open your project in Android Studio. Right click on your project and choose **Open Module Setting**


.. image:: images/open-module.png
   :width: 600
   :alt: Alternative text


2. Inside project Structure panel, choose **Depedencies** . Inside Declare Depedencies section, click icon **plus (+)** and chosee **JAR Depedency**. 



.. image:: images/project-structure.png
   :width: 600
   :alt: Alternative text


3. Inside Add Jar/Aar Depedency panel, enter path of AIS Connector Libraray, for example /Users/labs247/Desktop/libAis.


.. image:: images/add-jar.png
   :width: 600
   :alt: Alternative text


**BLE Configuration**

1. BLE Permission

AIS Connector Library use Bluetooth Low Energy (BLE) to communicate with AIS Device. In order to use Bluetooth features in your application, you must declare the Bluetooth permission :code:`BLUETOOTH`. You need this permission to perform any Bluetooth communication, such as requesting a connection, accepting a connection, and transferring data.
	::

        <uses-permission android:name="android.permission.BLUETOOTH"/>
        <uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>

Then at run-time you can determine BLE availability by using :code:`PackageManager.hasSystemFeature():`

	::

		// Use this check to determine whether BLE is supported on the device. Then
		// you can selectively disable BLE-related features.
		if (!getPackageManager().hasSystemFeature(PackageManager.FEATURE_BLUETOOTH_LE)) {
    		Toast.makeText(this, R.string.ble_not_supported, Toast.LENGTH_SHORT).show();
    		finish();
		}

2. Get the :code:`BluetoothAdapter`

The :code:`BluetoothAdapter` is required for any and all Bluetooth activity. The :code:`BluetoothAdapter` represents the device's own Bluetooth adapter (the Bluetooth radio). There's one Bluetooth adapter for the entire system, and your application can interact with it using this object. The snippet below shows how to get the adapter. Note that this approach uses :code:`getSystemService()` to return an instance of :code:`BluetoothManager`, which is then used to get the adapter. Android 4.3 (API Level 18) introduces :code:`BluetoothManager:`

	::

		private BluetoothAdapter bluetoothAdapter;
		...
		// Initializes Bluetooth adapter.
		final BluetoothManager bluetoothManager =
        	(BluetoothManager) getSystemService(Context.BLUETOOTH_SERVICE);
		bluetoothAdapter = bluetoothManager.getAdapter();

3. Enable Bluetooth

Next, you need to ensure that Bluetooth is enabled. Call :code:`isEnabled()` to check whether Bluetooth is currently enabled. If this method returns false, then Bluetooth is disabled. The following snippet checks whether Bluetooth is enabled. If it isn't, the snippet displays an error prompting the user to go to Settings to enable Bluetooth:

	::

		// Ensures Bluetooth is available on the device and it is enabled. If not,
		// displays a dialog requesting user permission to enable Bluetooth.
		if (bluetoothAdapter == null || !bluetoothAdapter.isEnabled()) {
    		Intent enableBtIntent = new Intent(BluetoothAdapter.ACTION_REQUEST_ENABLE);
    		startActivityForResult(enableBtIntent, REQUEST_ENABLE_BT);
		}

4. Find BLE Devices


To find BLE devices, you use the :code:`startLeScan()` method. This method takes a :code:`BluetoothAdapter.LeScanCallback` as a parameter. You must implement this callback, because that is how scan results are returned. Because scanning is battery-intensive, you should observe the following guidelines:

- As soon as you find the desired device, stop scanning.
- Never scan on a loop, and set a time limit on your scan. A device that was previously available may have moved out of range, and continuing to scan drains the battery.

The following snippet shows how to start and stop a scan:

	::

		/**
		 * Activity for scanning and displaying available BLE devices.
		 */
		public class DeviceScanActivity extends ListActivity {

		    private BluetoothAdapter bluetoothAdapter;
		    private boolean mScanning;
		    private Handler handler;

		    // Stops scanning after 10 seconds.
		    private static final long SCAN_PERIOD = 10000;
		    ...
		    private void scanLeDevice(final boolean enable) {
		        if (enable) {
		            // Stops scanning after a pre-defined scan period.
		            handler.postDelayed(new Runnable() {
		                @Override
		                public void run() {
		                    mScanning = false;
		                    bluetoothAdapter.stopLeScan(leScanCallback);
		                }
		            }, SCAN_PERIOD);

		            mScanning = true;
		            bluetoothAdapter.startLeScan(leScanCallback);
		        } else {
		            mScanning = false;
		            bluetoothAdapter.stopLeScan(leScanCallback);
		        }
		        ...
		    }
		...
		}