<div style="display: flex; align-items: flex-start;"> <div style="max-width: 500px;"> <h2 style="margin-top: 0;"> 



<div style="display: flex; align-items: flex-start;"> <img src="https://github.com/user-attachments/assets/483456ee-6c3e-492e-add3-3cda274fada8" alt="Tower United Illustration" style="width: 450px; height: auto; margin-right: 20px;"/> <div style="max-width: 500px;">
                        
                                                            
# 🔭 Features:
- Automated Data Gathering, Updating and Processing
- Player Lifecycle & Retention Metrics
- Interactive Dashboard 
- Statistical Modelling  
- Topic Clustering          


          
# 🔩 Technologies:
-  Google Forms
-  Google Sheets
-  Python/VADER/Beautifulsoup/Pandas/R
-  Power Bi 
-  SQL
-  DAX



# ♟️ The Process:
--
I wanted to create something that could automatically pull and process from my data scraping, so I developed a python script to pull from reviews left on steam, leveraged SQL to help deliver a cleaned up master set of data, connecting Power Bi to said data to have it automatically update results every morning feeding them through the statistical modeling within Power Bi.

# 🗳️ What I have learnt:
Oddly when pulling API data it started the pull from June of 1970 and issued the time stamps in seconds passed from that date, I had to run a simple DAX calculation inserting a new colum for the date format so I could display the reviews in an array.

When setting up the data pipelines attempting to sutomate the run, I cant help but note that if anything were to be changed within the python script that cause more data to be pulled I would have to rework the end to end process to reflect this new data, meaning I would not be able to leave this running without issue, if Steam were to ever update the API tied to the reviews or introduce a new way of ranking reviews it would prevent the automation from functioning. 

I was Surprised to see that the particular API the Tower Unite developers use does not allow me to tap into the country where the reviews were posted from.


🎬 Preview:

https://github.com/user-attachments/assets/5b6eb3cc-d602-4bd6-a72c-7509c0367f7a

https://github.com/user-attachments/assets/f164a6f2-e807-4056-b643-3c1fcb694d6a

https://github.com/user-attachments/assets/06733f19-3ac5-4c23-8994-91f02f2a2efe

![Data Relations](https://github.com/user-attachments/assets/466799cd-6417-4131-a4f8-039a411b22eb)

**** Come up with a better way to showcase DAX/SQL



