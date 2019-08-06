.. AIS Connector Library documentation master file, created by
   sphinx-quickstart on Wed Jul 31 09:53:10 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

SOS Function
=================================================

This snippet below is sample how to send sos message

	::

		public void sendMessageSos(String message) {
		       //connection checking
		       if (!mConnected || !mEchoInitialized) {
		           return;
		       }

		       //Get characteristic
		       BluetoothGattCharacteristic characteristic = BluetoothUtils.findEchoCharacteristic(mGatt);
		       if (characteristic == null) {
		           logError("Unable to find echo characteristic.");
		           disconnectGattServer();
		           return;
		       }

		       //matches value that acceptable by sensor
		       if (message.matches("^[\\x20-\\x5F]{0,16}")){
		           String messageSos = StringUtils.structurMsgSos(message); 
		           byte[] messageBytes = StringUtils.bytesFromString(messageSos);
		           if (messageBytes.length == 0) {
		               logError("Unable to convert message to bytes");
		               return;
		           }

		           //sending value to sensor
		           characteristic.setValue(messageBytes);
		           boolean success = mGatt.writeCharacteristic(characteristic);

		           //check status of sending process (Optional)
		           if (success) {
		               log("Wrote: " + StringUtils.byteArrayInHexFormat(messageBytes));
		           } else {
		               logError("Failed to write data");
		           }
		       } else {
		           logError("Character more 16 and not ascii");

		       }

		   }

The details explanation will be updated soon

