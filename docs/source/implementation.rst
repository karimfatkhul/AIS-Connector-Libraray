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

The following snippet shows how to start a scan:

	::

		private void startScan() {
		        if (!((MainActivity) getActivity()).hasPermissions()|| mScanning) {
		            return;
		        }
		        ...
		        mScanResults = new HashMap<>();
		        mScanCallback = new BtleScanCallback(mScanResults);

		        mBluetoothLeScanner = mBluetoothAdapter.getBluetoothLeScanner();

		        // Note: Filtering does not work the same (or at all) on most devices. It also is unable to
		        // search for a mask or anything less than a full UUID.
		        // Unless the full UUID of the server is known, manual filtering may be necessary.
		        // For example, when looking for a brand of device that contains a char sequence in the UUID
		        ScanFilter scanFilter = new ScanFilter.Builder()
		                .setServiceUuid(new ParcelUuid(SERVICE_UUID))
		                .build();
		        List<ScanFilter> filters = new ArrayList<>();
		        filters.add(scanFilter);

		        ScanSettings settings = new ScanSettings.Builder()
		                .setScanMode(ScanSettings.SCAN_MODE_LOW_POWER)
		                .build();

		        mBluetoothLeScanner.startScan(filters, settings, mScanCallback);

		        mHandler = new Handler();
		        mHandler.postDelayed(this::stopScan, SCAN_PERIOD);

		        mScanning = true;
		        log("Started scanning.");
		    }

The following snippet shows how to start a scan:

	::

		private void stopScan() {
		        if (mScanning && mBluetoothAdapter != null && mBluetoothAdapter.isEnabled() && mBluetoothLeScanner != null) {
		            mBluetoothLeScanner.stopScan(mScanCallback);
		            scanComplete();
		        }

		        mScanCallback = null;
		        mScanning = false;
		        mHandler = null;
		        log("Stopped scanning.");
		    }


You can see the result status using :code:`scanComplete()`

	::

		private void scanComplete() {
		        if (mScanResults.isEmpty()) {
		            return;
		        }

		        for (String deviceAddress : mScanResults.keySet()) {
		            BluetoothDevice device = mScanResults.get(deviceAddress);
		            GattServerViewModel viewModel = new GattServerViewModel(device);

		            ViewGattServerBinding binding = DataBindingUtil.inflate(LayoutInflater.from(getContext()),
		                    R.layout.view_gatt_server,
		                    mBinding.serverListContainer,
		                    true);

		            binding.setViewModel(viewModel);
		            binding.connectGattServerButton.setOnClickListener(v -> connectDevice(device));
		        }
		    }


If you want to scan for only specific types of peripherals, you can instead call startLeScan(UUID[], BluetoothAdapter.LeScanCallback), providing an array of UUID objects that specify the GATT services your app supports.

Here is an implementation of the BluetoothAdapter.LeScanCallback, which is the interface used to deliver BLE scan results:

	::

		private class BtleScanCallback extends ScanCallback {

		        private Map<String, BluetoothDevice> mScanResults;

		        BtleScanCallback(Map<String, BluetoothDevice> scanResults) {
		            mScanResults = scanResults;
		        }

		        @Override
		        public void onScanResult(int callbackType, ScanResult result) {
		            addScanResult(result);
		        }

		        @Override
		        public void onBatchScanResults(List<ScanResult> results) {
		            for (ScanResult result : results) {
		                addScanResult(result);
		            }
		        }

		        @Override
		        public void onScanFailed(int errorCode) {
		            logError("BLE Scan Failed with code " + errorCode);
		        }

		        private void addScanResult(ScanResult result) {
		            BluetoothDevice device = result.getDevice();
		            String deviceAddress = device.getAddress();
		            mScanResults.put(deviceAddress, device);
		        }
		    }

5. Connect to GATT Server

The first step in interacting with a BLE device is connecting to it— more specifically, connecting to the GATT server on the device. To connect to a GATT server on a BLE device, you use the :code:`connectGatt()` method. This method takes three parameters: a :code:`Context` object, :code:`autoConnect` (boolean indicating whether to automatically connect to the BLE device as soon as it becomes available), and a reference to a :code:`BluetoothGattCallback:`

	::

		private void connectDevice(BluetoothDevice device) {
		       GattClientCallback gattClientCallback = new GattClientCallback();
		       mGatt = device.connectGatt(getContext(), false, gattClientCallback);
		   }

This connects to the GATT server hosted by the BLE device, and returns a :code:`BluetoothGatt` instance, which you can then use to conduct GATT client operations. The caller (the Android app) is the GATT client. The :code:`BluetoothGattCallback` is used to deliver results to the client, such as connection status, as well as any further GATT client operations.