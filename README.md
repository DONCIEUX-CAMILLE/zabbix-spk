## zabbix-spk

#Zabbix Synology spk for different models
#Create the env :

#Download the good toolchain on sourgeforge.net https://sourceforge.net/projects/dsgpl/files/Tool%20Chain/
#Put the tgz in spksrc/distrib/toolchains/7.0

# name in syno-$arch-$version
mkdir spksrc/toolchains/syno-$arch-$version
cd spksrc/toolchains/syno-$arch-$version
mkdir work
# until working…
chmod -R u+w * && tar -Jxf spksrc/distrib/toolchains/7.0/xxx.txz -C work
# Create Makefile in the folder spksrc/toolchains/syno-$arch-$version


