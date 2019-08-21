.. AIS Connector Library documentation master file, created by
   sphinx-quickstart on Wed Jul 31 09:53:10 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. _sos_function:

SOS Function
=================================================

Use :code:`messageSos(message)` method to send SOS message to AIS Devices

This snippet bellow is sample usage of this method

	::

		btnSendSos = (Button) view.findViewById(R.id.send_sos_button);

		        sendSos.setOnClickListener(new View.OnClickListener()
		        {
		            @Override
		            public void onClick(View v)
		            {
		                String message ="BENCANA ALAM";
		                 ble.sendMessageSos(message);

		            }
		        });

There are 2 return message type of the method above.

**1. Confirm**
  Confirm indicate that sos message is send sucsessfully.

**2. Ignore**
  Ignore indicate that ais device is not responding during the sending sos process.

This snippet bellow is code to check return message of :code:`messageSos(message)` method.

 	::

 		DataManager.getInstance().getString(KeyConstants.KEY_STATUS_SOS, KeyConstants.KEY_STRING_DEFAULT)

SOS message system have a timeout of around 500 ms should be employed in the mobile application side while waiting for the above response. If the timeout is exceeded, the request should be repeated until the response above is acquired. After the request is confirmed, there is a 10 s timeout before the actual switching to AIS-SART mode truly begins. In this time frame, the user can request to cancel the switching action using this method :code:`sendCancelMessageSos()` 