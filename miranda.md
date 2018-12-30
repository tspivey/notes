# Notes on Miranda-ng

Homepage: https://www.miranda-ng.org/en/

## Installation for best accessibility

Going from the 64-bit portable version, these instructions should mostly apply for the installed one.

* Extract the 7z, it doesn't create a directory. I put it in c:\miranda.
* New user profile, the first field is profile name, the label it has is not very good.
* Both an account creator and import wizard pop up, close the import wizard
* Add jabber account, fill out the details. Miranda beeped when I typed the @ but still let me type, but don't do that. Fill the user and domain out separately.
* OK out of that.

Needed plugins (from the plugin updater, system tray > main menu > Available components list):

* clist _blind - gives a more accessible contact list
*MirOTR - OTR messaging
* Scriver - accessible message field, the default one should also work but not sure if it autoreads

Install those, but don't go to the plugins options page. The newly installed plugins won't be there. Instead, restart miranda first, then do that.

Check clist_blind, mirotr and scriver. Uncheck tabsrmm and toptoolbar. Everything but toptoolbar was already set properly, but I unchecked that because it gave an annoying message when miranda started.

## Default hotkeys
* Control shift A shows the window and hides it again.
* Control shift i shows the last conversation with unread messages.

## Problems

### Error -1: Unknown error
Server-to-server connection failed: connection-timeout

This error might pop up every so often.
To fix:

* Status menu, Jabber, Services..., XML Console
* Wait a while until this comes back. The last few lines of the XML console should say who wasn't found. Delete that contact, and repeat until the message goes away.
