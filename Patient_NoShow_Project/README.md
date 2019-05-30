# Patient_NoShow_Project: Investigating a dataset 

This is part of Udacity Data Analyst Nanodegree program. We were given a dataset and the aim is to investigate the dataset, using techniques we have learnt

## Introduction 
This report investigates the dataset collected from 100k medical appointments in Brazil in order to explore which factors have affected the rate of 'no show-up' for their scheduled medical appointment. 
The original dataset contains 110,527 rows and 14 columns 

## Overview
I first investigated the dataset to find any missig/odd data, and clean the dataset. Based on the clean dataset, Exploratory Data Analysis was conducted, and the findings were presented 

## Key findings 
Through the data wrangling process, we learnt that some patients made more than one appointment and some entries contain errors or were duplicated. After cleaning the data, total 110,520 entries were saved and used for the analysis. The exploratory data analysis shows that more female patients have made appointments and also missed their appointments more than male patients. It also shows that among patients who made an appointment at the hospital, a relatively high proportion of them was younger ones (10.6% aged 5 or under) although the average patient age was 37 years old. Moreover, the exploratory analysis shows that young (below 15) and middle age (50-60) patients had the highest records of show-up, while nearly the equal number of patients across the age group below 60 failed to turn up. Finally, the time difference between the scheduled day and appointment day has been calculated and added to the existing dataset, and the finding shows that the appointment was more likely missed if there is a long waiting time until their appointment.
