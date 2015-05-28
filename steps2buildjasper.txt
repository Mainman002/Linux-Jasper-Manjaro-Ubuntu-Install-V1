1) Setup your system to get the source and build the code
From your distribution's repository, install the following if you don't already have them:



for ubuntu type this in the terminal:

sudo apt-get install git wget subversion make autoconf automake libtool gfortran g++ python2.7 espeak python-pip bison

########################


for manjaro Linux / arch Linux:

sudo pacman -S git wget subversion make autoconf automake libtool espeak python-pip bison gcc-fortran python2-numpy


manjaro has a few issues working around errors in make. . .

###########################


else manually install these

git
wget 
subversion
make
autoconf
automake
libtool
gfortran
g++
python2.7
python-pip
espeak (TTS)

2) Create a directory to hold jasper and it's dependent software
   (and change into the directory)

mkdir ~/jasper && cd ~/jasper

3) Get the Jasper code

git clone https://github.com/jasperproject/jasper-client.git jasper

4) Use pip to setup python for building jasper

sudo pip install --upgrade setuptools
sudo pip install -r jasper/client/requirements.txt

5) Build Pocketsphinx (STT)

   Pocketsphinx requires sphinxbase, so do that first

wget http://downloads.sourceforge.net/project/cmusphinx/sphinxbase/0.8/sphinxbase-0.8.tar.gz
tar -zxvf sphinxbase-0.8.tar.gz
cd sphinxbase-0.8
./configure --enable-fixed
make -j4 #(4 core)
sudo make install
cd ../

    Now for pocketsphinx

wget http://downloads.sourceforge.net/project/cmusphinx/pocketsphinx/0.8/pocketsphinx-0.8.tar.gz
tar -zxvf pocketsphinx-0.8.tar.gz
cd pocketsphinx-0.8
./configure
make -j4 #(4 core)
sudo make install
cd ../


6) Build CMUCLMTK

svn co https://svn.code.sf.net/p/cmusphinx/code/trunk/cmuclmtk/
cd cmuclmtk
./autogen.sh
make -j4 #(4 core)
sudo make install
cd ../

7) Build OpenFST

wget http://distfiles.macports.org/openfst/openfst-1.3.3.tar.gz
tar -xvf openfst-1.3.3.tar.gz
cd openfst-1.3.3
./configure --enable-compact-fsts --enable-const-fsts --enable-far --enable-lookahead-fsts --enable-pdt
make -j4 #(4 core)
sudo make install
cd ../

8) Build m2m-aligner

wget https://m2m-aligner.googlecode.com/files/m2m-aligner-1.2.tar.gz
tar -xvf m2m-aligner-1.2.tar.gz
cd m2m-aligner-1.2
make -j4 #(4 core)
cd ../

9) Build the MIT Language Modeling Toolkit

wget https://mitlm.googlecode.com/files/mitlm-0.4.1.tar.gz
tar -xvf mitlm-0.4.1.tar.gz
cd mitlm-0.4.1
./configure
make -j4 #(for 4 core)
sudo make install
cd ../



Now, build the dependencies we need for the phonetisaurus:


10) Build the Phonetisaurus (finally!)

wget https://phonetisaurus.googlecode.com/files/phonetisaurus-0.7.8.tgz
tar -xvf phonetisaurus-0.7.8.tgz
cd phonetisaurus-0.7.8
cd src
make
cd ../../

11) Copy m2m-aligner & phonetisaurus-g2p to your /usr/local/bin directory

sudo cp m2m-aligner-1.2/m2m-aligner /usr/local/bin/
sudo cp phonetisaurus-0.7.8/phonetisaurus-g2p /usr/local/bin/

12) Build the Phonetisaurus FST model
wget http://phonetisaurus.googlecode.com/files/g014b2b.tgz
tar -xvf g014b2b.tgz
cd g014b2b

export LD_LIBRARY_PATH=/usr/local/lib/

./compile-fst.sh
cd ..

13) Setup your Jasper profile

cd jasper/client
python populate.py

first name #Don't skip this

last name #you should skip this

gmail account #you should skip this

password for gmail #you should skip this

stt_engine sphinx

cd ../../

   This creates ~/.jasper/profile.yml, which can be edited to your liking

14) Start jasper

cd jasper
./jasper.py

fix the errors left over Lol


create a file called profile.yml in

~/jasper/ then enter this text into it


carrier: ''
first_name: Test
gmail_password:
last_name: 
phone_number: ''
location: '00000'
prefers_email: false
stt_engine: sphinx
pocketsphinx:
  fst_model: '/home/'Your User Name'/phonetisaurus/g014b2b.fst'
  hmm_dir: '/usr/local/share/pocketsphinx/model/hmm/en_US/hub4wsj_sc_8k'
tts_engine: espeak-tts
espeak-tts:
  voice: 'en-rp+f5'
  pitch_adjustment: 80
  words_per_minute: 150



then save that file and try to run jasper


locate:  jasper/client/diagnose.py

then gedit it and edit line 19 the one that says "def check_network_connection"to:

to have a server="localhost"

then remove the diagnose.pyc file and then


