# NPK-detection
#!/usr/bin/python
import urllib2
import cookielib
from getpass import getpass
import sys
import os
import spidev
import time
import RPi.GPIO as GPIO
from stat import *
#Define Variables
delay = 1
ldr_channel = 0
GPIO.setmode(GPIO.BOARD)
GPIO.setup(3,GPIO.OUT)
GPIO.setup(5,GPIO.OUT)
GPIO.setup(7,GPIO.OUT)
GPIO.setup(11,GPIO.OUT)
GPIO.setup(13,GPIO.OUT)
GPIO.setup(15,GPIO.OUT)
#Create SPI
spi = spidev.SpiDev()
spi.open(0, 0)
 
def readadc(adcnum):
    # read SPI data from the MCP3008, 8 channels in total
    if adcnum > 7 or adcnum < 0:
        return -1
    r = spi.xfer2([1, 8 + adcnum << 4, 0])
    data = ((r[1] & 3) << 8) + r[2]
    return data
GPIO.output(3,True)
GPIO.output(5,True)
GPIO.output(7,True)
GPIO.output(11,True)
GPIO.output(13,True)
GPIO.output(15,True)
ldr_value = readadc(ldr_channel)
print "---------------------------------------"
print("LDR Value: %d" % ldr_value)
time.sleep(delay)
    
Y=ldr_value
if((Y>625)and(Y<=630)):
  n="L"
elif((Y>630)and(Y<=640)):
  n="M"
elif(Y>640):
  n="H"
if((Y>=630)and(Y<=635)):
  ph="L"
elif((Y>635)and(Y<=645)):
  ph="M"
elif(Y>645):
  ph="H"
if((Y>=640)and(Y<=645)):
  po="L"
elif((Y>645)and(Y<=655)):
  po="M"
elif(Y>655):
  po="H"
if((n=="L")and(ph=="L")and(po=="L")):
   x=" COMPLEX FERTILIZER " 
elif((n=="L")and(ph=="L")and(po=="M")):
   x=" UREA FERTILIZER + DIAMMONIUM PHOSPHATE FERTILIZER "  
elif((n=="L")and(ph=="L")and(po=="H")):
   x=" UREA FERTILIZER + DIAMMONIUM PHOSPHATE FERTILIZER "
elif((n=="L")and(ph=="M")and(po=="L")):
   x=" UREA FERTILIZER + POTASSIUM DICHROMATE FERTILIZER "
elif((n=="L")and(ph=="M")and(po=="M")):
   x=" UREA FERTILIZER " 
elif((n=="L")and(ph=="M")and(po=="H")):
   x=" UREA FERTILIZER "
elif((n=="L")and(ph=="H")and(po=="L")):
   x=" UREA FERTILIZER + POTASSIUM DICHROMATE FERTILIZER "
elif((n=="L")and(ph=="H")and(po=="M")):
   x=" UREA FERTILIZER "
elif((n=="M")and(ph=="L")and(po=="L")):
   x=" DIAMMONIUM PHOSPHATE FERTILIZER + POTTASSIUM DICHROMATE FERTILIZER"
elif((n=="M")and(ph=="L")and(po=="M")):
   x=" DIAMMONIUM PHOSPHATE FERTILIZER "
elif((n=="M")and(ph=="L")and(po=="H")):
   x=" UREA + DIAMMONIUM PHOSPHATE FERTILIZER "
elif((n=="M")and(ph=="M")and(po=="L")):
   x=" UREA + POTTASSIUM DICHROMATE FERTILIZER "
elif((n=="M")and(ph=="M")and(po=="M")):
   x=" COMPLEX FERTILIZER "
elif((n=="M")and(ph=="H")and(po=="L")):
   x=" UREA FERTILIZER "
elif((n=="M")and(ph=="H")and(po=="M")):
   x=" UREA FERTILIZER "
elif((n=="M")and(ph=="H")and(po=="H")):
   x=" UREA FERTILIZER "
elif((n=="M")and(ph=="M")and(po=="H")):
   x=" UREA FERTILIZER "
elif((n=="L")and(ph=="H")and(po=="H")):
   x=" UREA FERTILIZER "
elif((n=="H")and(ph=="L")and(po=="L")):
   x=" DIAMMONIUM PHOSPHATE FERTILIZER + POTTASSIUM DICHROMATE FERTILIZER "
elif((n=="H")and(ph=="L")and(po=="M")):
   x=" DIAMMONIUM PHOSPHATE FERTILIZER "
elif((n=="H")and(ph=="L")and(po=="H")):
   x=" DIAMMONIUM PHOSPHATE FERTILIZER "
elif((n=="H")and(ph=="M")and(po=="L")):
   x=" DIAMMONIUM PHOSPHATE FERTILIZER +  POTTASSIUM DICHROMATE FERTILIZER "
elif((n=="H")and(ph=="M")and(po=="M")):
   x=" DIAMMONIUM PHOSPHATE FERTILIZER + POTTASSIUM DICHROMATE FERTILIZER "
elif((n=="H")and(ph=="M")and(po=="H")):
   x=" DIAMMONIUM PHOSPATE FERTILIZER + POTTASSIUM DICHROMATE FERTILIZER "
elif((n=="H")and(ph=="H")and(po=="L")):
   x=" POTTASSIUM DICHROMATE FERTILIZER "
elif((n=="H")and(ph=="H")and(po=="M")):
   x=" POTTASSIUM DICHROMATE FERTILIZER "
elif((n=="H")and(ph=="H")and(po=="H")):
   x=" NO NEED OF FERTILIZERS "

message =x
number = "8778622497"

if __name__ == "__main__":    
    username = "8760757986"
    passwd = "1532977471"

    message = "+".join(message.split(' '))

 #logging into the sms site
    url ='http://site24.way2sms.com/Login1.action?'
    data = 'username='+username+'&password='+passwd+'&Submit=Sign+in'

 #For cookies

    cj= cookielib.CookieJar()
    opener = urllib2.build_opener(urllib2.HTTPCookieProcessor(cj))

 #Adding header details
    opener.addheaders=[('User-Agent','Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/37.0.2062.120')]
    try:
        usock =opener.open(url, data)
    except IOError:
        print "error"
        #return()

    jession_id =str(cj).split('~')[1].split(' ')[0]
    send_sms_url = 'http://site24.way2sms.com/smstoss.action?'
    send_sms_data = 'ssaction=ss&Token='+jession_id+'&mobile='+number+'&message='+message+'&msgLen=136'
    opener.addheaders=[('Referer', 'http://site25.way2sms.com/sendSMS?Token='+jession_id)]
    try:
        sms_sent_page = opener.open(send_sms_url,send_sms_data)
    except IOError:
        print "error"
        #return()

    print "success" 
    #return ()
