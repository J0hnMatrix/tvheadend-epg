# tvheadend-epg

This repo contain a step-by-step guide for enabling EPG on TVHeadend from a xml file The xml is provided by Racacax: https://www.racacaxtv.ga/

Better to use the root account:
sudo su -

Update the package list on your TVHeadend server:
sudo apt update

Install the following packages:
sudo apt install nano

sudo apt install dos2unix

Edit the script file (create a new file):
nano /usr/bin/tv_grab_file

Paste the following content:

  #!/bin/sh
  # tv_grab_file for remote XMLTV file
  dflag=
  vflag=
  cflag=
  if [ $# = 0 ]
  then
      wget -q -O - "https://allfrtv.gq/xmltv/xmltv.xml"
      exit 0
  fi

  for arg
  do
  delim=""
  case "$arg" in
  #translate --gnu-long-options to -g (short options)
      --description) args="${args}-d ";;
      --version) args="${args}-v ";;
      --capabilities) args="${args}-c ";;
      #pass through anything else
  esac
  done

  eval set -- $args

  while getopts "dvc" option
  do
      case $option in
          d)  dflag=1;;
          v)  vflag=1;;
          c)  cflag=1;;
          \?) printf "unknown option: -%s\n" $OPTARG
              printf "Usage: %s: [--description] [--version] [--capabilities] \n" $(basename $0)
              exit 2
              ;;
          esac >&2
  done

  if [ "$dflag" ]
  then
      printf "Racacax"
  fi
  if [ "$vflag" ]
  then
      printf "1.0\n"
  fi
  if [ "$cflag" ]
  then
      printf "baseline\n"
  fi

  exit 0
Changing permissions on the script file:

chmod a+rx /usr/bin/tv_grab_file

Run the script:
/usr/bin/tv_grab_file

If an error appear, try to convert with DOS2Unix:
dos2unix /usr/bin/tv_grab_file

If everything is good, reboot the server...

Login to the TVHeadend webui and do the following:

  Configuration 
      > General
      > Base 
      > Select "Expert" in the "User interface level"
      > Click on "Save"

  Configuration
      > Channel / EPG
          > EPG Grabber modules
          > Internal: XMLTV: Racacax
          > Click on "Enabled" on the left side panel
          > Change the priority if required
          > Click on "Save"

  Configuration
      > Channel / EPG
          > Map services
              > Map all services
In the "EPG source" field, you should be able to select your channel from the drop down menu.

If not, reboot the server.
