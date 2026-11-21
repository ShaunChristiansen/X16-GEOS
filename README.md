# GEOS 2.0 for the Commander X16

The Commodore 64 GEOS 2.0 kernel (from the reverse-engineered source at
github.com/mist64/geos), rebuilt as an X16 cartridge. It runs the C64's own
deskTop, geoWrite, geoPaint and desk accessories unchanged from C64 disk
images (D64 or D81, up to three drives, kept on the X16's SD card or the
emulator's folder), in colour as on a C64, with a wallpaper behind the
deskTop (five more to choose from in GeosWallpapers, with the WALLPAPER
program). It prints with GEOS's own
printer drivers, to a printer on the X16's IEC port or to a file, and
geoTelnet and GeoTerm reach BBSs through TexElec's serial and WiFi card.
Tested in x16emu R49 with the R49 ROM.

## Running it (Windows)

Put these in one folder:

    geosx16.bin      the cartridge (ROM banks 32-34)
    GEOSDISK.D81     drive A: a GEOS 2.0 disk image (must hold DESK TOP)
    GEOSDSK2.D81     drive B (optional): any GEOS disk image
    GEOSDSK3.D81     drive C (optional, needs a drive B): any GEOS disk image
                     (each drive can be a .D81 or a .D64, see below)
    GEOSWALL.BMX     the deskTop's wallpaper, a BMX picture (optional; more in GeosWallpapers)
    BASIC.BIN        the C64's BASIC ROM (optional, for the calculator and geoCalc, see below)
    KERNAL.BIN       the C64's KERNAL ROM (optional, as above)
    GEOS-X16.bat     the launcher
    GEOS-X16.PRG     the loader for a real X16 (see "Real hardware")
    GeosWallpapers   five more wallpapers, for WALLPAPER (optional, see below)
    mkwall.py        makes a wallpaper from a picture (optional, see below)
    geosimg.py       makes GEOS D81s and copies files to them (optional)
    prnview.py       shows a print file as a picture of the page (optional,
                     see "Printing")

Then either copy `x16emu.exe` (with its `rom.bin`) into that folder, put the
folder inside the x16emu folder, or edit the `X16EMU` line in the .bat. Run
`GEOS-X16.bat`. It starts:

    x16emu -cartbin geosx16.bin -rtc -scale 2 -capture

- `-capture` gives the mouse to the emulator, so the GEOS pointer follows
  every movement and pushing it against a screen edge works (geoWrite and
  geoPaint scroll that way). Ctrl+M releases and recaptures the mouse,
  as the window title says.
- `-rtc` gives GEOS the PC's date and time.
- Use x16emu without an SD card image: the disk images are read from the
  emulator's current folder (its host file system, device 8). With an SD
  card image (or on a real X16), they must be on the card instead.
- Each drive can be a D64 (a 1541 disk, 165K) or a D81 (a 1581 disk,
  790K): GEOSDISK.D64 instead of GEOSDISK.D81, and so on. All three can be
  D81s. When a drive has both, the .D64 is used, so take the .D64 out of
  the folder when you change a drive to a .D81.
- The disk images stay on the disk (SD card or folder): GEOS reads and
  writes their blocks there, through a cache in the X16's banked RAM. A
  standard 512K X16 is enough. An image that can only be read (a read-only
  file) works too; the boot screen says "(READ-ONLY)" and GEOS reports an
  error when it tries to write to it.

The shipped disks: drive A has DESK TOP, geoWrite, the calculator, note pad,
preference manager, pad color manager, six fonts and colour settings (170
blocks free). Drive B has geoPaint, photo manager and three
fonts (370 blocks free). Run geoPaint from drive B so its pictures go there.
Drive C (GEOSDSK3.D81, a 1581 disk) holds all the programs (geoWrite,
geoPaint, geoCalc, geoSpell with its dictionary, geoMerge, Text Grabber,
geoText, geoLaser, ...), the desk accessories, fonts and forms (300K
free).
It has DESK TOP too, so it can also be drive A on its own: rename it
GEOSDISK.D81 (and take GEOSDISK.D64 out of the folder). geosimg.py is the
disk tool (see "Using it").

## Real hardware: GEOS-X16.PRG

On a real X16, GEOS-X16 needs a cartridge (or an expansion card) with RAM
in the cartridge banks: 32-34 and 48-56. A ROM cartridge is not enough,
because GEOS works in those banks. Put these together on the SD card, in its
root folder or in a folder of their own: GEOS-X16.PRG, geosx16.bin, the disk
images (GEOSDISK.D81 etc.) and, if you have them, BASIC.BIN and KERNAL.BIN
(see "The calculator, geoCalc and the C64 ROMs"). Then, in X16 BASIC (with
DOS"CD:FOLDER" first if they are in a folder):

    LOAD "GEOS-X16.PRG",8
    RUN

It checks the cartridge's RAM, copies geosx16.bin into it and starts GEOS
the way the X16 starts a cartridge after a reset, but without a reset, so
GEOS finds its disks in the same folder. Without RAM in banks 32-34 and
48-56 it says so and stays in BASIC.

- The cartridge's RAM keeps GEOS over a reset, not over power-off: after
  switching on, LOAD and RUN it again. After a reset the X16 starts GEOS
  from the cartridge by itself, but then looks for its disks in the card's
  root folder.
- To get to X16 BASIC past the cartridge, hold SHIFT while switching on or
  resetting (ROM R49 and later).
- To try it in x16emu, start it with an empty RAM cartridge instead of
  -cartbin: LOADER-TEST.bat runs `x16emu -cart ramcart.crt` (made with
  x16emu's `makecart -ram 32 63 -o ramcart.crt`; tools/ramcart.crt), then
  LOAD and RUN as above.
- Tested in x16emu, with the files in the emulator's folder and on an SD
  card image. Not yet on a real X16.

## The calculator, geoCalc and the C64 ROMs

The calculator and geoCalc do their maths with the floating point routines
in the C64's BASIC ROM, which the X16 does not have: the calculator for
everything, geoCalc for all but adding up (turning what you type into a
number and back, powers, SQRT, LN, LOG, EXP, SIN, COS, TAN, ATAN). Put
your own copies of the C64's BASIC and KERNAL ROMs next to the disk images,
as BASIC.BIN and KERNAL.BIN (8K each; VICE has them in its C64 folder as
basic-901226-01.bin and kernal-901227-03.bin), and the boot screen says
"C64 ROMS: BASIC.BIN, KERNAL.BIN": both then work as on a C64, with the
same results to the last digit. Without them they open and nothing
crashes, but they do not calculate: the calculator's display stays empty.
GEOS-X16 cannot include the ROMs (Commodore's copyright).

## Printing

GEOS-X16 prints with GEOS's own printer drivers, as a C64 does: choose
the printer on the deskTop, then print from geoWrite, geoPaint or any other
program.

- A printer driver must be on drive A when GEOS starts. The deskTop shows
  its icon in the bottom left corner ("Printer NOT ON DISK" when there is
  none). The GEOS 2.0 disk (GEOS64.D64) has three: MPS-803, Star
  NL-10(com) and ASCII Only. Copy one onto your drive A, with the deskTop
  (drag it from another drive) or with geosimg.py:

      python geosimg.py cp GEOS64.D64 GEOSDISK.D81 "MPS-803"

  With more than one on the disk, double-click a printer icon to choose it.
- A printer on the IEC port (the X16's Commodore serial port, as on a C64)
  on device 4: GEOS-X16 sends it what a C64 would send, through the X16
  KERNAL. Commodore printers (MPS-801/803 and compatibles), and Epson-type
  printers with a Commodore serial interface, work with the drivers made
  for them: MPS-803 for a Commodore printer (or one set to Commodore
  mode), an Epson driver (Star NL-10(com), or an Epson FX driver from
  another GEOS disk) for one in Epson mode.
- No printer (always so in x16emu, which has none): the same bytes go to
  a file next to the disk images instead, PRINT1.PRN for the first print
  job, PRINT2.PRN for the next, and so on (from 1 again at every boot;
  an old file of the same name is replaced). A job's file is finished 5
  seconds after the printing stops. prnview.py (Python 3 with Pillow) shows
  what a printer would make of it:

      python prnview.py PRINT1.PRN          -> PRINT1.png

  It knows Commodore bit image graphics (MPS-803 driver), Epson ESC/P
  graphics (Star NL-10(com) and other Epson drivers) and plain text
  (ASCII Only).
- ASCII Only prints text only: choose Draft or NLQ in geoWrite's print
  box ("High" sends graphics, which that driver leaves out, as on a C64).
- Printers on the C64's user port (geoCable and other parallel cables)
  cannot work: their drivers drive the C64's CIA chip, which the X16 does
  not have.
- Tested in x16emu: geoWrite printing with all three drivers to print
  files, and with a test printer on device 4 (an x16emu patched to take
  the KERNAL's printer calls, tools/x16emu-testcards.patch), which got the
  same bytes. A real printer on a real X16 has not been tried yet.

## Terminals: geoTelnet and GeoTerm with TexElec's serial card

geoTelnet 1.3 (Bo Zimmerman's telnet program, made for a WiFi modem on the
C64's user port) and GeoTerm64 2.0 (a modem terminal, user port, 300/1200
baud) work with TexElec's Commander X16 serial and network card: its ESP32
WiFi modem (Zimodem firmware, the same as the C64Net WiFi modem
geoTelnet was written for) or its DB9 RS-232 port.

- On a C64 both drive the user port themselves, bit by bit through the CIA
  chip and its NMI. GEOS-X16 points those routines at the card's UART
  (TL16C2550) as the programs load: the patches are in memory only, and the
  programs on the disk do not change. Their own screens, menus, phonebook,
  capture buffer and XModem/Punter transfers stay as they were.
- Faster than a C64: the card's UART does the bit timing, with a FIFO and
  hardware flow control (RTS/CTS: the ESP32 waits while GEOS is busy, so
  nothing is lost). The WiFi side runs at 115,200 baud whatever the
  program's baud menu says. geoTelnet's screen keeps up with about 1,100
  characters a second (it read only (baud code + 1) * 2 bytes a frame; now
  up to 48), GeoTerm with about 1,600 (x16emu, a test BBS sending 300 lines
  at once).
- Dialling: the ESP32 takes Zimodem's AT commands, so a BBS is dialled by
  name: ATDT then the address and port, e.g. ATDToasisbbs.hopto.org:6400 for
  The Oasis BBS. geoTelnet's telnet menu and phonebook do the same. A first
  time with the card, set up its WiFi with Zimodem's own commands, as for
  any Zimodem modem (its documentation: github.com/bozimmerman/Zimodem).
- The DB9 port instead: put GEOSSER.CFG next to the disk images, one line:
  DB9 (the program's own baud rate) or DB9 9600 (any of 300, 1200, 2400,
  4800, 9600, 19200, 38400, 57600, 115200). WIFI (or no file) is the ESP32.
  A speed after WIFI sets the ESP32 link too, if its Zimodem was set to
  another rate.
- The card must be at its default address ($9FE0, DIP switches IO7 low).
- geoTelnet comes as CVT files on its disk: geosimg.py puts it on a GEOS
  disk ("geosimg.py cvtfrom GEOSDISK.D81 geotelnet-v1.3.d64 GEOTELNET.CVT").
- Not tested on a real card yet: tested in x16emu with its emulated UART
  card connected to a stand-in for the ESP32 and a BBS (tools/fakebbs.py,
  tools/x16emu-testcards.patch). Neither needs the C64 ROM files.

## Terminals with the Calypso card

geoTelnet and GeoTerm also work with the Calypso card (Wavicle's "super
peripheral" on top of VERA, firmware by smrobtzz): its WiFi, or its DB9
RS-232 port.

- Calypso has no modem, just network sockets, so GEOS-X16 is the modem:
  type AT (OK) and ATDT then the address and port, e.g.
  ATDToasisbbs.hopto.org:6400 (port 23 if there is none). GEOS-X16 looks
  the name up through the card, connects, and answers CONNECT (or NO
  CARRIER). Commands echo, as Zimodem's do. Online, +++ goes back to
  commands: ATH hangs up, ATO goes back online. When the BBS hangs up:
  NO CARRIER. geoTelnet's telnet menu and phonebook dial the same way.
- Set up the card's WiFi with its own tools first (its setup guide), as
  with any Calypso program.
- GEOS-X16 uses the card's own network code, NET.BIN, from its firmware: a
  file on the card's DOS device. At start it loads NET.BIN from device 8.
  If your Calypso is not device 8, copy NET.BIN from its device onto your
  SD card, next to geosx16.bin.
- Which card: GEOS-X16 uses Calypso when NET.BIN loaded and there is no
  TexElec card. With both, put GEOSSER.CFG next to the disk images with
  one line: CALYPSO.
- The DB9 port instead: CALYPSO DB9, or CALYPSO DB9 19200 (9600 up to
  921600; with no speed, 9600, or 19200 when the program asks for 14,400
  or more). Hardware flow control (RTS/CTS).
- Speed: up to 64 bytes a frame from the card (about 3,800 characters a
  second), more than geoTelnet's and GeoTerm's screens draw.
- Not tested on a real Calypso yet: tested in x16emu (which has no Calypso)
  with a stand-in NET.BIN (tools/fakenet: the same calls, its socket the
  emulator's UART and a test BBS). The Calypso's custom ROM (R49, "Calypso
  edition") runs GEOS-X16: all the tests pass with it.

## Colour and wallpaper

GEOS 2.0 on a C64 gives every 8x8 card of the screen its own two colours.
GEOS-X16 shows them all, as a C64 does: the preference manager's colour
swatches, the pad color manager, the deskTop's coloured file window and
icons, geoPaint's colour pictures.

- The shipped drive A sets white on blue for the screen (menus, dialog
  boxes, applications), a white file window with an icon colour for each
  kind of file, a yellow pointer and a light blue border. These are GEOS's
  own settings files ("Preferences" and "Pad Color Pref"), as the two
  managers save them.
- To change the screen colours: geos menu > preference manager. Click a
  swatch (BORDER, B.GROUND, F.GROUND, MOUSE) to step through the colours,
  CHANGE to apply them, SAVE to keep them, EXIT.
- To change the file window's colours: geos menu > pad color manager. Click
  a swatch to step its colour, or DEFAULT 1/2/3 for a ready set; SAVE,
  EXIT. COLOR OFF goes back to plain screen colours.
- The wallpaper (an X16 extra) replaces the deskTop's grey background
  pattern: wherever the deskTop shows its pattern in the screen colours,
  the picture in GEOSWALL.BMX shows instead; the file window, icons, menus
  and dialog boxes stay on top. Applications (geoWrite, geoPaint, ...) do
  not show it. No GEOSWALL.BMX in the folder: no wallpaper.
- Wallpapers are BMX files, the Commander X16's own bitmap format, so
  they can come from X16 paint programs and converters too. A BMX works
  if it has 256 colours (8 bits a pixel), is 320 pixels wide and at least
  200 high (a taller one loses rows at the top and bottom equally), and is
  uncompressed. GEOS keeps 64 of the 256 palette entries for its own
  colours (entry 0 and entries 16c+1 to 16c+3): pixels in those get the
  nearest of the picture's other colours. Pictures made with mkwall.py
  leave those entries alone, so nothing changes in them.
- More wallpapers: the GeosWallpapers folder has five (Synthwave, Space,
  Mountains, Tropical, Aurora), each a .BMX with a .png picture of it.
  Keep the folder next to geosx16.bin and choose one with WALLPAPER (below),
  or copy one into the GEOS-X16 folder as GEOSWALL.BMX (see its README.txt).
- WALLPAPER, a GEOS program for GEOS-X16 (apps/wallpaper.s): double-click
  it on the deskTop and choose your own GEOSWALL.BMX, one of the five in
  GeosWallpapers, or no wallpaper; "*" marks the one now. It goes back to
  the deskTop, which shows your choice at once, and the choice is kept
  for the next boot in GEOSWALL.CFG (a one-line text file next to the disk
  images: the wallpaper's file name, or NONE). Without GEOSWALL.CFG the boot
  uses GEOSWALL.BMX. Put it on a disk with
  `python tools/mkwallapp.py GEOSDISK.D81` (it assembles apps/wallpaper.s
  with ca65/ld65 when they are there, else uses the ready-built
  apps/wallpaper.bin): it becomes the 8th
  file, on the deskTop's first page (the file that was there moves to the
  end). On a C64's GEOS it only says that it is for GEOS-X16.
- Your own wallpaper: `python mkwall.py picture.png GEOSWALL.BMX` (needs
  Python 3 with Pillow) scales any picture to 320x200 and converts it to
  the 207 colours GEOS leaves free. `python mkwall.py --default
  GEOSWALL.BMX` makes the shipped one again. A wallpaper in the older
  GEOSWALL.BIN format (before BMX) converts with `python mkwall.py
  --frombin GEOSWALL.BIN GEOSWALL.BMX`.
- The mouse pointer has a thin black outline (also an X16 extra), so it
  shows on any colour.

## Sounds

GEOS-X16 makes small sounds: a soft click when you press the mouse button
(a softer one when you let go), a quick "flick" when a menu drops down, a
swish when a dialog box opens and a shorter one when it closes.  They use
one voice of VERA's sound chip (PSG voice 15), so programs keep the
YM2151 and the other 15 voices to themselves.

To turn them off, put a file called GEOSSND.CFG next to geosx16.bin with
one line in it:

    OFF

Delete the file (or write ON) to turn them back on.  The setting is read
when GEOS starts.

## SNES controllers

An SNES controller in joystick port 1 or 2 moves the GEOS pointer, as a
mouse does (the mouse still works too):

- D-pad: the pointer. It starts slow (a pixel a frame) and speeds up the
  longer you hold it (2, then 4 pixels a frame).
- Y held: slow, a pixel a frame, for exact places.
- A or B: the mouse button (hold it to drag; twice quickly: a double
  click).

## The SD card: SD CARD

SD CARD (on drive A) is a window on the X16's SD card: the folder
geosx16.bin is in, and every folder in it.

- It lists every file and folder there with its size (folders end in "/").
  Pick a folder to go in; "Up a folder" comes back.  Eight a page:
  "More..." for the next ones.
- Pick a file for what can be done with it:
  - .CVT or .TXT: "Copy it onto a GEOS disk" (a .CVT is the GEOS file as
    it was, a .TXT becomes a geoWrite document);
  - .D81 or .D64: "Put it in a drive" (kept for next time in
    GEOSDRV.CFG); the deskTop comes back and reads it;
  - .BMX: "Make it the wallpaper" (as WALLPAPER does, kept in
    GEOSWALL.CFG);
  - any file: "Rename it", "Delete it" (it asks first).
- "Copy a GEOS file here" copies a file from a GEOS disk into the folder
  shown (NAME.CVT, a geoWrite document also as NAME.TXT).
- "Back to the deskTop" goes back to geosx16.bin's folder first.

### Copying files

- A .CVT file is the usual way GEOS programs and documents travel on the
  internet (made by GEOS's CONVERT and the like). Any GEOS file comes in
  whole: sequential or VLIR, its icon, header and date as they were.
- A .TXT file (plain text, from your PC say) becomes a geoWrite document of
  the same name, 50 lines a page (a long line counts as more), in BSW 9.
  Windows (CR LF) and Unix (LF) lines both work; a tab becomes spaces.
- Out to the card: any GEOS file as NAME.CVT (the whole file, ready for
  another GEOS or an emulator), and a geoWrite document also as NAME.TXT
  (just its text, CR LF lines).
- Files are copied a block at a time, so a file of any size works (the
  disk's room is the limit). A file of that name already on the GEOS disk:
  not copied (delete it first). An SD card file of that name: replaced.
- If it fails, its box says why with an error number (GEOS's disk errors;
  $80 not a CVT file, $81 the SD card file wouldn't open, $82 already
  there).
- tools/cvtmake.py makes .CVT files from a disk image on your PC (and
  checks them).

### Disk images in the drives

- GEOS-X16 checks the image first (that it opens, its header, its last
  block), so a bad one leaves the drive as it was; then the old image
  closes, the new one opens, and the deskTop comes back and reads it.
- Same type only: a D81 into a drive with a D81 in it, a D64 into a D64
  drive (GEOS's drive type stays as it started). All three drives are D81s
  in the usual set-up.
- A disk already in a drive can't go into a second one.
- Drive A needs a deskTop on it, as always: put a disk without one into
  drive A and GEOS asks for one when it goes back to the deskTop.
- Kept for next time: the drives' images go into GEOSDRV.CFG (next to
  geosx16.bin, a line per drive), and GEOS-X16 opens those when it starts
  (one that isn't there or won't open: the usual one instead). For the
  usual disks (GEOSDISK, GEOSDSK2, GEOSDSK3) again, delete GEOSDRV.CFG
  (SD CARD's "Delete it" does).
- Programs can do the same: GEOS-X16 calls 10 (drive X = 8-10, r0 = the
  image's file name) and 11 (drive X's image name -> (r0)); see
  inc/geosx16.inc.

### A folder as drive C

"Make this folder drive C" turns the folder shown into a GEOS disk in
drive C, so the deskTop shows its files in its own window: open them,
drag them to and from drives A and B, delete them, as on any disk.

- GEOSSD.D81 (a blank D81, next to geosx16.bin) goes in drive C, emptied,
  and every file of the folder is copied onto it: a .CVT as its GEOS file,
  a .TXT as a geoWrite document, any other file as a plain (non-GEOS)
  file, its bytes as they are.  Files that don't fit (a D81 image, say)
  are left out; folders in it too.  Drive C needs a D81 (GEOSDSK3.D81).
- Drive C stays the folder, through restarts too, until it is saved back.
- Then, with drive C still the folder, SD CARD shows "Save drive C back to
  its folder": changed files are written over their SD files (a .CVT as a
  .CVT, a .TXT from the geoWrite document, any other file as its bytes),
  new ones are added (NAME.CVT, a plain file as NAME), and files deleted
  on drive C are deleted from the folder.  It asks whether to put drive
  C's own disk back.
- What came from where is kept in GEOSSD.LST in the folder (not shown) and
  GEOSSD.CFG next to geosx16.bin.  At most 40 files.
- Changes reach the SD card only when you save them back.

## Hybrid programs: the X16 KERNAL from a GEOS program

A GEOS program can use the X16's own KERNAL, and so the SD card (any file,
any folder), while GEOS keeps drawing its screen: GEOS-X16 call 9.

- The program puts a 5-byte block in its memory: the routine's address (any
  routine in the X16 KERNAL ROM), then A, X and Y for it. Then X/Y = the
  block, A = 9, JSR $FF7D. GEOS-X16 swaps GEOS out and the X16 KERNAL in,
  calls the routine (carry clear: MACPTR and MCIOUT step through memory),
  swaps GEOS back, and hands back the routine's A, X, Y and carry (also
  written into the block).
- inc/geosx16.inc makes it one line: X16K X16K_OPEN (A, X, Y as for OPEN),
  with X16K_CODE once in the program. It also names the other GEOS-X16
  calls and the usual KERNAL routines.
- Zero page $02-$7F (GEOS's r0-r15 among it) reaches the X16 KERNAL as it
  is, so calls that take r0-r15 work too.
- Rules: file names and buffers below $A000; files 10-14 (GEOS-X16 keeps
  2-6 and 15 open: the disk images, printing, the command channel); CLRCHN
  before handing control back to GEOS (its own disk access resets the
  channels). Interrupts are off during each call, so a big load pauses the
  mouse for that long.
- Check first: "X16G" at $FF78 and a version of 2 or more at $FF7C (on a C64
  those bytes are the C64 KERNAL's). Version 3 adds calls 10 and 11 (a disk image in a drive, as SD CARD does).
- X16 FILES (apps/x16files.s, tools/mkx16app.py puts it on a disk) is the
  example: it reads HELLO.TXT from the SD card, writes X16CALL.OUT next to
  it, and shows the first line in a GEOS dialog box.

## Using it

- Mouse: the X16 mouse (left button).
- Keyboard: the PC keys type what they show. GEOS's Commodore-key shortcuts
  (C=+letter in menus) are Alt+letter or Windows key+letter. Ctrl+letter
  gives the control codes. Cursor keys, Delete/Backspace, Return, Home as on
  the C64. Held keys repeat at GEOS's own rate.
- Two drives: the deskTop shows drive B when GEOSDSK2 is there. Drag a
  file onto the other drive's icon to copy it. File dialogs have a Drive
  button (it switches between A and B).
- Drive C: GEOS 2.0's deskTop shows drive C's icon below the other two
  but works with two drives at a time, as on a C64. To use drive C, drag
  its icon onto drive A's or drive B's icon: the two swap places, and the
  disk that was in drive C is open as drive A or B. Drag the drive C icon
  onto the same drive again to swap them back. (GEOS 2.0 wants a RAM
  expansion for a third drive; GEOS-X16 has one built in.)
- Saving: every block GEOS writes is written straight into the image file
  on the disk, so documents survive a crash or a closed window.
- Leaving: deskTop "options > BASIC" closes the disk images and returns to
  X16 BASIC (READY.), without resetting the X16: as C64 GEOS does, it puts
  the KERNAL's memory, interrupts and screen back the way they are at
  power-on and starts BASIC, so the SD card's current folder stays. A reset
  of the X16 starts GEOS again (in x16emu: Ctrl+M to release the mouse,
  then Ctrl+R).
- Your disks: any GEOS 2.0 disk image works as GEOSDISK.D64 (or .D81) if
  it holds DESK TOP. Keep a copy of the original. A disk needs some free
  space, or geoWrite and geoPaint refuse to run ("Disk near full").
- A D81 for GEOS: geosimg.py (Python 3) makes one ("geosimg.py new APPS.D81
  "Disk Name" ID") and copies GEOS files into it from D64s or other D81s
  ("geosimg.py cp FROM.D64 APPS.D81 GEOWRITE GEOPAINT ..."), with their
  icons and VLIR records. The deskTop can also copy files onto it.

## Programs tried

Run from C64 disk images in x16emu R49 (each opened from the deskTop and
used a little: a document typed, a picture drawn, an album made, ...). The
2.x programs and desk accessories below were also all opened from one D81:

- Work: deskTop 2.0, CONFIGURE 2.0, geoWrite 2.1 and 1.3, geoPaint 2.0
  and 1.3, geoSpell 1.1, geoMerge 2.1, geoLaser 2.0 and 2.1 (not with its
  LaserWriter), Text Grabber 2.3, geoText 1.0, CONVERT 2.5, PAINT DRIVERS,
  note pad, alarm clock, preference mgr, pad color mgr, photo
  manager 2.1 and 1.3, text manager 2.1 and 1.3.
- The calculator and geoCalc 1.0, with BASIC.BIN and KERNAL.BIN (see "The
  calculator, geoCalc and the C64 ROMs"): new sheets, formulas with SQRT, LN, LOG, EXP, SIN, COS, TAN,
  ATAN, powers, SUM, RND; errors (LN(0), SQRT(-1)) show *RANGE* as they
  should; the "New Car" sample sheet's payments are right to the cent.
  Without the ROMs it stops at its first calculation.
- Graphics Grabber 2.0 starts (it loads into page 3, over the X16's
  interrupt entry; the kernel deals with that, see "How it works").
- Copy protection: geoWrite 2.0 and Graphics Grabber 2.0 copies that were
  installed on another GEOS system ask for "the GEOS disk used the first
  time" and stop, as they would on a C64.
- Damaged files: a disk image can hold a broken copy (a bad sector, a
  broken block chain); such a program cannot work on a C64 either.
- C64 BASIC programs on a GEOS disk (DISK COPY, BACKUP, ...): opening one
  leaves GEOS for X16 BASIC (a C64 program cannot run on the X16).

## What is not there (yet)

- Printing on a user-port printer (geoCable): see "Printing".
- No RAM disk (the built-in RAM expansion is 64K, for GEOS's own use).
- Input drivers: the X16 mouse driver is built in. Do not load another
  input driver from the deskTop.
- The alarm clock desk accessory's alarm does not ring (the C64's CIA alarm
  is not emulated). The clock itself runs.
- 1541 and 1581 disks only: D64 (35 tracks) and D81. No D71 (1571), no
  40-track D64.
- The deskTop's "format" and "erase" need a real drive's DOS: use
  geosimg.py to make an empty disk instead.
- Real hardware: needs an X16 cartridge with RAM, see "Real hardware:
  GEOS-X16.PRG" (the screen code keeps its variables in bank 33, GEOS its
  kernel in 48-55, geoCalc's maths uses 56). 512 KB of banked RAM is
  enough. Only x16emu has been tested.

## How it works (for the curious)

- The cartridge boot code (bank 32) opens the disk images on device 8
  (GEOSDISK, GEOSDSK2, GEOSDSK3: .D64, else .D81) on channels 2-4 for
  reading and writing (or reading only), loads the wallpaper (the one
  GEOSWALL.CFG names, else GEOSWALL.BMX) into video RAM, sets VERA up
  (two tile layers and sprites), copies the 16 KB
  GEOS kernel into cartridge RAM banks 48-55 and starts GEOS. It looks for
  each image in read mode first: CMDR-DOS's modify mode would make an
  empty file for an image that is not there.
- The C64's $01 banking value is used as the X16 ROM bank: $30-$37 are the
  eight copies of the kernel; banks 53-55 also hold a shadow of the C64's
  I/O area for GEOS to write VIC/CIA registers into.
- The screen: VERA layer 1 holds the C64 bitmap ($A000, in RAM bank 47)
  as 1000 tiles of 2 bits per pixel, one per 8x8 card, each with the card's
  foreground colour (its map entry's palette offset); bit 0 pixels are
  transparent. Layer 0 has a solid tile in each card's background colour,
  or, on the deskTop, the wallpaper's tile for that card. Every frame the
  VSYNC interrupt converts the card rows GEOS has drawn into (and a few
  rows of the colour matrix at $8C00, and one more row when its checksum
  changed or it was converted while being drawn), turns the VIC sprite registers into VERA sprites, reads keys
  and mouse packets from the SMC, and ticks the CIA time-of-day clock.
- The wallpaper's BMX file is read 8 rows at a time (with MACPTR) into
  banked RAM and written to video RAM as layer 0's tiles 0-999, one per
  8x8 card, then its colours; GEOS's own palette entries are remapped on
  the way (kernal/x16/prn.s: LoadBmx).
- The wallpaper on the deskTop: its background pattern is a 50% checkerboard.
  In cards in the screen colours, the pixels that differ from it belong to
  something drawn on top; each card's runs of such lines are drawn from
  their leftmost to their rightmost differing column (an icon or a label
  may contain pattern pixels too), everything else is transparent.
- The disk driver ($9000) serves every drive. Its directory code is
  GEOS's 1541 and 1581 drivers' (it goes by the drive type: a D64 is a
  1541, a D81 a 1581), its BAM routines sit in the helper bank (bank 33),
  and $9C80-$9D7F stays free for a 1581's second BAM block, which the
  deskTop uses directly. Block reads and writes are new: a block is read
  from the image file through a cache of one disk line per RAM bank
  (banks 3-14: a D64 track or half a D81 track, read with one seek and
  MACPTR); a written block goes to the file at once (the X16 KERNAL's "P"
  positioning command, then 256 bytes) and into the cached line. Reading
  a file's last byte makes the DOS close it, so each image's last block
  is kept in RAM (bank 2) and only ever written.
- The RAM expansion: GEOS's StashRAM, FetchRAM, SwapRAM and VerifyRAM
  work on 64K of the X16's banked RAM (banks 15-22). GEOS 2.0's deskTop
  only looks for drive C when there is an expansion with the disk drivers
  "in it"; it swaps drive C in with ChangeDiskDevice, which here just moves
  an image to another device number. SetDevice has no driver to fetch, as
  the one driver serves all drives.
- The X16 sends interrupts through RAM at $038B, which the deskTop uses
  for variables and clears when it starts (and again after a desk
  accessory, with interrupts on). The kernel puts the entry back every
  time round the main loop, and replaces the deskTop's clearing loop with
  one that keeps it, when it starts the deskTop. geoCalc swaps its zero
  page with $0334-$03AE: its swap loop is pointed at a copy that leaves
  the entry alone. A program loaded over the entry (Graphics Grabber
  loads at $0340) gets the entry back at the end of the load, and the
  program's own three bytes there are kept aside; when the program itself
  runs into $038B (no interrupt has mapped ROM bank 0), the kernel does
  what those bytes do and goes on after them. All of this happens in
  memory: the programs on the disk are not changed.
- C64 KERNAL calls made by applications and printer drivers reach a
  table at $FF81 in the kernel. Printer calls (LISTEN, SECOND, CIOUT,
  UNLSN, and OPEN/CHKOUT/CHROUT/CLOSE, for devices 4-7) go to the printer
  code in RAM bank 25 (kernal/x16/prn.s). LISTEN (or OPEN) asks the X16
  KERNAL for the printer on the IEC port; if nothing answers, the job goes
  to the next PRINTn.PRN file on device 8 instead. The bytes are gathered
  in a 4K buffer and passed on together (before the next bus call, when
  the buffer is full, and for a file when the job ends), because each trip
  to the X16 KERNAL swaps its zero page and $0200-$03FF with GEOS's. The
  VSYNC interrupt counts the frames since the last printing and ends a
  file's job after 5 seconds. Calls for other devices (a disk command
  channel, for instance) answer "device not present".
- GEOS-X16 calls for GEOS programs: the kernel has "X16G" and a version
  byte at $FF78 (check them first: a C64 has something else there), then a
  JMP at $FF7D, with A = the call. Call 0: r0 = a wallpaper's file name
  (0-terminated, below $A000, CMDR-DOS style, e.g.
  "/GeosWallpapers/:SPACE.BMX", a BMX picture as above), or r0 = 0 for
  none: the file is loaded
  into video RAM (the screen code redraws every row when the wallpaper
  goes on or off) and the choice is written to GEOSWALL.CFG; C = 1 (A = 1)
  when the file is missing or is not a wallpaper. Call 1: the current
  wallpaper's file name -> (r0) ("" for none). The code is in RAM bank 25
  with the printer code (kernal/x16/prn.s).
- Known programs' patches: after every ReadFile (the end of loading any
  file, VLIR records included) a table in RAM bank 25 is checked
  (kernal/x16/apppatch.inc): where memory holds a known program's bytes at
  a known address, they are replaced. geoTelnet's MODmodem library
  (ComInit, ComSend, ComGet, ... in its record 1, from its source) and
  GeoTerm's NMI driver become calls to the serial code (serial.s): the UART
  directly, no interrupts, 8N1, FIFOs and auto RTS/CTS on, received bytes
  into a 512-byte ring (or straight into GeoTerm's own receive buffer),
  emptied when the program asks and once a frame. The GEOS-X16 calls
  ($FF7D) 2-8 are open, close, send, receive, carrier, pump and pump now.
  With the Calypso card, those calls go on to calypso.s in RAM bank 26,
  next to the card's NET.BIN (loaded there at boot): a 16-byte trampoline
  in zero page switches to that bank, with interrupts off. NET.BIN's
  arguments live in zero page $02-$1B (GEOS's r0-r13) and it sets $02B3
  while it talks to the card, so those are kept and put back around each
  of its calls. The card is asked for new bytes only when a program asks
  (never from the interrupt: the card shares VERA's SPI with the SD card),
  at most once a frame. GeoTerm asks for the carrier from its interrupt:
  that answer comes from what calypso.s said last.
  The same table makes geoTelnet's PAL/NTSC test and its send routine's
  raster wait (both watch VIC registers that GEOS-X16 only shadows) answer
  at once.
- Call 10 (another disk image in a drive) is three parts: a KERNAL job
  that checks the image on file 9 and then reopens the drive's channel with
  it (mount.s, RAM bank 26, through CalGo), then the helper bank (it can
  switch RAM banks; mount.s's bank can't) copies the image's last block to
  where the disk driver keeps it, drops the drive's tracks from the cache,
  sets the open/write bits and keeps the name for call 11 (sdio.s: H_Mount).
  The programs' patch table (apppatch.inc) and its scanner live in RAM bank
  26 too, beside calypso.s, called through CalGo after every ReadFile.
- Call 9 (an X16 KERNAL routine for a program) is a job like the
  printer's: X16_KRun swaps the X16 KERNAL's zero page ($80-$A8) and
  $0200-$03FF in, maps ROM bank 0 and calls it (prn.s: KCall, JKCall); the
  KERNAL's own state (its open files) is kept for the next call.
- The screen's background check: once a frame one card row's bitmap is
  summed (a Fletcher sum: two bytes), and the row is converted again when
  the sum changed, which catches drawing that did not go through GEOS's
  routines (geoTelnet scrolls by moving the bitmap itself). It used to be a
  rotate-and-add sum, which came out the same for every row of text.
- geoCalc's maths: the boot loads BASIC.BIN into RAM bank 23 and puts
  KERNAL.BIN, behind a copy of GEOS's $C000-$DFFF, into cartridge bank 56:
  the C64's memory with $01 = $37 (BASIC and KERNAL ROM in). When geoCalc
  is loaded, its two instructions that switch the ROMs in and out ("lda
  #$37 / sta $01" and "lda $2FF3 / sta $01") become calls to GEOS-X16
  code that maps RAM bank 23 at $A000 and bank 56 at $C000, and back. geoCalc
  keeps interrupts off in between, and its own error handler (BASIC's
  error vector at $0300) still catches overflows. As with the other
  patches, the program on the disk is not changed.
- SNES controllers (kernal/x16/joy.s, RAM bank 25): once a frame, from
  the same interrupt, ports 1 and 2 are read on VIA 1 port A as the X16
  KERNAL's joystick_scan does (PA2 latch, PA3 clock, PA7/PA6 data; the
  I2C lines on PA0-1 left alone). The D-pad adds to the mouse's movement
  (msumx, msumy, as the mouse's packets do) and A/B set joybtn, which the
  mouse code ORs into its button.
- Sounds (kernal/x16/snd.s, RAM bank 25): once a frame the code compares
  GEOS's mouse button (mouseData) with the last frame's and plays a ~2 ms
  noise burst on a change. DoMenu0 (a menu drops down), DoDlgBox and
  RstrFrmDialogue each leave a request byte that it turns into a few
  frames of pitch or noise sweep. It saves and restores VERA's address
  port, so it never disturbs a program's own VERA access.

## Building

Needs cc65 (ca65/ld65) and python3:

    ./build_x16.sh          -> geosx16.bin

Tests (Linux, x16emu R49 + ROM, Xvfb, xdotool, ImageMagick, python3-pil):

    ./test_x16.sh           boot, geoWrite: create, type, font, save; reopen; BASIC
                            (DISK=WORK.D81 ./test_x16.sh: the same from a D81)
    ./test_x16_typing.sh    22 lines typed at 60+ keys/s; key repeat
    ./test_x16_drives.sh    two drives: copy a file from B to A, check both BAMs
    ./test_x16_da.sh        preference manager opened and closed twice
    ./test_x16_colour.sh    colours, the preference manager's swatches, the
                            wallpaper on the deskTop (not in geoWrite);
                            this test and test_x16.sh also check from memory
                            dumps that the screen shows exactly what GEOS
                            drew (tools/x16vramcheck.py)
    ./test_x16_print.sh     a printer driver from GEOS64.D64; geoWrite prints
                            twice: PRINT1.PRN and PRINT2.PRN, the same bytes
                            (with X16PRN=file and an x16emu built with
                            tools/x16emu-testcards.patch: to the test
                            printer on device 4 instead)
    ./test_x16_wallpaper.sh WALLPAPER on drive A: Space (kept after a new
                            boot), none, a missing folder (an error box),
                            Aurora, your own GEOSWALL.BMX; GEOSWALL.CFG each time
    ./test_x16_x16call.sh   call 9: X16 FILES reads HELLO.TXT and writes
                            X16CALL.OUT through the X16 KERNAL; OK back to
                            the deskTop; no HELLO.TXT: its box; once more
    ./test_all_x16.sh       all eight
    ./test_x16_sdcard.sh    SD CARD: folders, copying, a disk image in a
                            drive, wallpaper, rename, delete
    ./test_x16_sddrive.sh   SD CARD: a folder as drive C, saved back
    ./test_x16_basic.sh     options > BASIC: X16 BASIC, really running
                            (COLOR 1,2:CLS turns the screen red)
    ./test_x16_loader.sh    GEOS-X16.PRG on an empty RAM cartridge (x16emu's
                            makecart): from the emulator's folder, no
                            cartridge, no RAM in banks 48-56, from an SD
                            card image and from a folder of it
    ./test_x16_serial.sh    geoTelnet and GeoTerm with the serial card: an
                            x16emu built with tools/x16emu-testcards.patch
                            (its UART card connected to tools/fakebbs.py),
                            your geoTelnet disk and a disk with GeoTerm64
    ./test_x16_joy.sh       SNES controller: an x16emu built with
                            tools/x16emu-testcards.patch (X16JOY: a file
                            stands in for controller 1); A opens a menu,
                            the D-pad moves the pointer (slow with Y,
                            faster when held), memory dumps checked
    ./test_x16_calypso.sh   the same with the Calypso card: tools/fakenet's
                            NET.BIN (a stand-in) in front of that UART, the
                            BBS raw (GEOS-X16 is the modem)
    ./mkdist.sh             dist/GEOS-X16 (+ .zip): cartridge, launcher, disks,
                            wallpapers, prnview.py

Print files: tools/prnview.py (a .PRN as a picture of the page).
Terminal test: tools/fakebbs.py (a stand-in ESP32 modem and BBS).
Wallpapers: tools/wallpapers.py draws the five in GeosWallpapers;
tools/mkwall.py converts any picture; tools/mkwallapp.py builds WALLPAPER
and puts it on a disk image (tools/geosimg.py add puts any GEOS
sequential file on one).

Disk tools: tools/geosimg.py (D64 and D81: make an empty GEOS disk, copy
GEOS files between images, add CVT files or a new sequential GEOS file,
list, check a BAM against the files). In
../tools: d64ls.py (list), d64cp.py (copy files between D64s, set the disk
name), d64rm.py (delete), d64check.py (check a D64's BAM against the
files), geoscolours.py (write the colour settings files).

Licence: the GEOS kernel source is the reverse-engineered GEOS by Maciej
Witkowiak and Michael Steil (github.com/mist64/geos). GEOS itself and its
applications are Berkeley Softworks / GeoWorks software: use your own disks.
