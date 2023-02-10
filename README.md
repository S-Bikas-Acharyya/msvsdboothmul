# Week 0 (I will update it properly today 11-02-'23) (For now I wrote vaguely)

To adequately utilize the open source skywater130 pdk and understand the design flow, we first require to install all the tools, which are
1. Xschem
2. Magic
3. Ngspice
4. Netgen
5. Open_PDK
Note: open_pdk has to be installed last so it can correctly associate the xschem and magic directories. Note: if the configure step fails during any process, its most likely due to missing additional packages, and they need to be installed (preferably from source) to complete the installation

# Installing Magic

Open the terminal and execute the following command 

'''

$  git clone git://opencircuitdesign.com/magic
$  cd magic
$	 ./configure
$  make
$  sudo make install

'''

More info can be found at http://opencircuitdesign.com/magic/index.html

# Installing Netgen

Open the terminal and execute the following commands

'''
$  git clone git://opencircuitdesign.com/netgen
$  cd netgen
$	./configure
$  make
$  sudo make install
'''

More info can be found at http://opencircuitdesign.com/netgen/index.html

# Installing Xschem

'''
$  git clone https://github.com/StefanSchippers/xschem.git xschem_git
$	./configure
$  make
$  sudo make install
'''

# Installing Ngspice

'''
 $ tar -zxvf ngspice-37.tar.gz
 $ cd ngspice-37
 $ mkdir release
 $ cd release
 $ ../configure  --with-x --with-readline=yes --disable-debug
 $ make
 $ sudo make install
'''

More info can be found at https://ngspice.sourceforge.io/index.html

# Installing Open_PDK

'''
$  git clone git://opencircuitdesign.com/open_pdks
$  open_pdks
$	./configure --enable-sky130-pdk
$  make
$  sudo make install
'''

More information available at https://github.com/google/skywater-pdk

# Working at Schematic

At first the working directory should have a file present "xschemrc" which can be available at ''' /usr/local/share/pdk/sky130A/libs.tech/xschem/xschemrc ''' . Just copy that file and paste in your working directory. Now open the terminal and type '''xschem''' The welcome window will pop up with all the necessary sky130 process. You can search the Youtube for research.
Now its time to make a schematic. At first I made a simple schematic with no hierarchy

![1_inverter schematic](https://user-images.githubusercontent.com/124414710/218174376-c330aecd-9b42-4037-aeff-4262e74d4d71.JPG)

Performing the DC analysis to get the VTC

![2_schematic_vtc](https://user-images.githubusercontent.com/124414710/218174563-fc803644-9c6b-4a57-b92a-c28040ee07ab.JPG)

To get an hierarchy it is suggested to use only the pin diagram with no voltage source ground etc.

![3_pins](https://user-images.githubusercontent.com/124414710/218174945-fa9efe0c-cac6-41b9-8b4d-0c8bf6f83cc2.JPG)

after generating the symbol and using it to get the schematic

![3_symbol](https://user-images.githubusercontent.com/124414710/218175108-7ea46080-82a9-4306-b637-1fc253a5d791.JPG)

now no need to run the dc operation get the transient response


![5_transient](https://user-images.githubusercontent.com/124414710/218175305-0ef71fd4-fc3d-440d-8e86-e8f801f45916.JPG)


the generated netlist

'''
For transient response

** sch_path: /home/vlsi-labs/Downloads/xschem_git/inverter/tb.sch
**.subckt tb out
*.opin out
x1 VCC VIN out VSS inv_not_final
V1 VCC VSS 1.8
.save i(v1)
V2 VIN VSS pulse 0 1.8 1n 1n 1n 4n 10n
.save i(v2)
V3 VSS GND 0
.save i(v3)
**** begin user architecture code

.lib ~/Downloads/open_pdks/sky130/sky130A/libs.tech/ngspice/sky130.lib.spice tt

.control

        tran 0 50n
	plot V(out), V(VIN)
.endc

.save all

**** end user architecture code
**.ends

* expanding   symbol:  inv_not_final.sym # of pins=4
** sym_path: /home/vlsi-labs/Downloads/xschem_git/inverter/inv_not_final.sym
** sch_path: /home/vlsi-labs/Downloads/xschem_git/inverter/inv_not_final.sch
.subckt inv_not_final VCC VIN out VSS
*.opin out
*.ipin VCC
*.ipin VSS
*.ipin VIN
XM34 out VIN VSS VSS sky130_fd_pr__nfet_01v8 L=0.15 W='1 * 1 ' nf=1 ad='int((nf+1)/2) * W / nf * 0.29'
+ as='int((nf+2)/2) * W / nf * 0.29' pd='2*int((nf+1)/2) * (W / nf + 0.29)' ps='2*int((nf+2)/2) * (W / nf + 0.29)'
+ nrd='0.29 / W ' nrs='0.29 / W ' sa=0 sb=0 sd=0 mult=1 m=1
XM42 out VIN VCC VCC sky130_fd_pr__pfet_01v8 L=0.15 W='1 * 1 ' nf=1 ad='int((nf+1)/2) * W / nf * 0.29'
+ as='int((nf+2)/2) * W / nf * 0.29' pd='2*int((nf+1)/2) * (W / nf + 0.29)' ps='2*int((nf+2)/2) * (W / nf + 0.29)'
+ nrd='0.29 / W ' nrs='0.29 / W ' sa=0 sb=0 sd=0 mult=1 m=1
.ends

.GLOBAL GND
.end


For dc response


** sch_path: /home/vlsi-labs/Downloads/xschem_git/inverter/tb.sch
**.subckt tb out
*.opin out
x1 VCC VIN out VSS inv_not_final
V1 VCC VSS 1.8
.save i(v1)
V2 VIN VSS 0
.save i(v2)
V3 VSS GND 0
.save i(v3)
**** begin user architecture code



.lib ~/Downloads/open_pdks/sky130/sky130A/libs.tech/ngspice/sky130.lib.spice tt

.control

        dc V2 0 1.8 0.01
	plot V(out), V(VIN)



.endc

.save all








**** end user architecture code
**.ends

* expanding   symbol:  inv_not_final.sym # of pins=4
** sym_path: /home/vlsi-labs/Downloads/xschem_git/inverter/inv_not_final.sym
** sch_path: /home/vlsi-labs/Downloads/xschem_git/inverter/inv_not_final.sch
.subckt inv_not_final VCC VIN out VSS
*.opin out
*.ipin VCC
*.ipin VSS
*.ipin VIN
XM34 out VIN VSS VSS sky130_fd_pr__nfet_01v8 L=0.15 W='1 * 1 ' nf=1 ad='int((nf+1)/2) * W / nf * 0.29'
+ as='int((nf+2)/2) * W / nf * 0.29' pd='2*int((nf+1)/2) * (W / nf + 0.29)' ps='2*int((nf+2)/2) * (W / nf + 0.29)'
+ nrd='0.29 / W ' nrs='0.29 / W ' sa=0 sb=0 sd=0 mult=1 m=1
XM42 out VIN VCC VCC sky130_fd_pr__pfet_01v8 L=0.15 W='1 * 1 ' nf=1 ad='int((nf+1)/2) * W / nf * 0.29'
+ as='int((nf+2)/2) * W / nf * 0.29' pd='2*int((nf+1)/2) * (W / nf + 0.29)' ps='2*int((nf+2)/2) * (W / nf + 0.29)'
+ nrd='0.29 / W ' nrs='0.29 / W ' sa=0 sb=0 sd=0 mult=1 m=1
.ends

.GLOBAL GND
.end

'''


# Creating the layout 

after having the netlist we use the netlist to generate partial layout


![1](https://user-images.githubusercontent.com/124414710/218175895-154edbd2-9b27-47ba-aad9-4ea18e0918a4.JPG)


## will update today .........................................!!!
