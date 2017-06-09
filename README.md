# modis_download
#download time series modis data

# -*- coding: utf-8 -*-
"""
Created on Mon Oct 10 16:51:31 2016

@author: lpalao
"""

#######
"""
Download modis from ftp server ladsweb.nascom.nasa.gov, "allData/6/MOD13Q1/2015/001/"
"""
#######

from ftplib import FTP
import os
import numpy as np
import glob

saveDir_ = 'D:\y_OtherSpatialData\MODIS_SR_NDVI\gtest' #'D:\y_OtherSpatialData\MODIS_SR_NDVI\Terra_16d'
if not os.path.exists(saveDir_):
    os.makedirs(saveDir_)
saveDir = os.path.abspath(saveDir_)

localDir = saveDir


def ftpDownloader(localDir, Dir, Tile,host="ladsweb.nascom.nasa.gov"):
    
    if not os.path.exists(localDir):
        os.makedirs(localDir)
    os.chdir(localDir)
    f=glob.glob("*.hdf")
    ftp=FTP(host)
    ftp.login()
    ftp.cwd(Dir)
    dataList = ftp.nlst()
    modData=[i for i in dataList if '%s'%Tile in i] 
    for data in modData:
        if data in f:
            continue

        else:
            print data
            with open(data,"wb") as Data:
                ftp.retrbinary('RETR %s' %data, Data.write)
            ftp.cwd("..")

version = 6
product = str(raw_input("Input MODIS Product [MOD13Q1, MYD13Q1, MOD11A2]: ")) #MOD11A2, MOD13Q1, MYD13Q1
StartYear=input("Input Start Year: ")
EndYear=input("Input End Year: ")
Year = np.arange(StartYear, EndYear+1) #[2015,2016]
DOY = np.arange(1,366,16)
Tile = str(raw_input("Input Tile [e.g., h29v08]: ")) #h29v08, h30v08, h29v07, h29v08, h30v07

for y in Year:
    for d in DOY:
        try:
            ftpDownloader(localDir,"allData/%s/%s/%s/%03d/" %(version,product,y,d), Tile)
        except:
            print "DOY %s is not existing" %(d)
