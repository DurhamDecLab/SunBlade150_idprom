# SunBlade150_idprom
Notes on reconfiguring the eeprom on a Sun Blade 150 with a flat battery.

I found various resources in different places including:
https://groups.google.com/g/comp.unix.solaris/c/XY0yoiyFaTc
https://github.com/MrSparc/idprom-repair
https://web.archive.org/web/20141005163815/http://www.squirrel.com/sun-nvram-hostid.faq.html#arcane

I've managed to get a working method for my 150 by using bits of all of them, so here it is. Power on the machine, and get the "invalid idprom" message. Do:

ok set-defaults
ok reset-all 

It will restart. Then:

ok show-devs

You're looking for the eeprom, then cd to that location, which for me is:

cd /pci@1f,0/ebus@c/eeprom@1,0

Type .properties and look for the address, here it is fff58000. You can find the location of this in memory:

ok fff58000 map?

Which is 1fff1000000, so map this:

ok 1fff1000000 0 0 map-page

This seems to be where some systems vary, the command isn't map-page in some guides, but that's what works here. Then enter details:

[Start of IDPROM]\
ok 1 1fd8 c!

[Machine type 83 for Sun Blade 100 sun4u, byte 1 of serial #]\
ok 83 1fd9 c!

[Ethernet address]\
ok 0 1fda c!\
ok 3 1fdb c!\
ok ba 1fdc c!\
ok 9 1fdd c!\
ok 57 1fde c!\
ok d0 1fdf c!

[Date code]\
ok 0 1fe0 c! \
ok 0 1fe1 c!\
ok 3 1fe2 c!\
ok ba 1fe3 c!

[serial # bytes 2-4]\
ok 9 1fe4 c! \
ok 57 1fe5 c!\
ok d0 1fe6 c!

[Checksum] \
ok 82 1fe7 c! 

I cribbed these numbers from one of the links at the start, you can use other valid MAC address or serial but you'll need to work out the checksum - again, one of the other links mentioned that. Then do a reset-all, and it should come up without the warning, and showing a serial and network address. But if the battery is still dead, this will all be lost next time you shut down - you can apparently either buy a good eeprom, or modify the old one to take an external battery.
