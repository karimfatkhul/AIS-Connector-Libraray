.. AIS Connector Library documentation master file, created by
   sphinx-quickstart on Wed Jul 31 09:53:10 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. _tagging_function:

Tagging Function
=================================================

Use :code:`ble.sendMessageTagging(string_commodityCategory, string_commodityWeight);` method to send comodity tagging data to the AIS Device.

**Note** The maximum character that can be send to ais device both comodity category and comodity weight is 10 characters.

There are 2 return message type of the method above.

**1. Confirm**
  Confirm indicate that tagging message is send sucsessfully.

**2. Ignore**
  Ignore indicate that ais device is not responding during the sending tagging message process.

This snippet bellow is code to check return message of :code:`ble.sendMessageTagging(string_commodityCategory, string_commodityWeight);` method.

 	::

 		DataManager.getInstance().getString(KeyConstants.KEY_STATUS_TAGGING, KeyConstants.KEY_STRING_DEFAULT)
