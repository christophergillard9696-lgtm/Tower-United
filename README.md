<div style="display: flex; align-items: flex-start;"> <div style="max-width: 500px;"> <h2 style="margin-top: 0;"> 



<div style="display: flex; align-items: flex-start;"> <img src="https://github.com/user-attachments/assets/483456ee-6c3e-492e-add3-3cda274fada8" alt="Tower United Illustration" style="width: 900px; height: auto; margin-right: 20px;"/> <div style="max-width: 1000px;">
                        
                                                            
# 🔭 Features:
- Automated Data Gathering, Updating and Processing
- Player Lifecycle & Retention Metrics
- Interactive Dashboard 
- Statistical Modelling  
- Topic Clustering
- Review Sentimate Timeline         


          
# 🔩 Technologies:
-  Google Forms
-  Google Sheets
-  Python/VADER/Beautifulsoup/Pandas/R
-  Power Bi 
-  SQL
-  DAX



# ♟️ The Process:

I wanted to create something that could really unveil the community of Tower Unite, my plans are to eventually use this information to better inform my ethnographic piece based on the game community, by seeking out the wants, the needs, the folk lore, the habits and fears are what I'm motivated to find out. So to better understanding what people liked and did not like about the game was key to my research. Initially I brought people in from my field research to interview, google forms were produced and the Excel sheets were filled with their responses, eventually unioned and cleaned up with SQL, next was to scrape the reviews using python VADER to tap into the API for the data and then Beautifulsoup to draw out keyworks in the spectrum of reviews.

# 👁️‍🗨️ Insights:
-The Main Dashboard includes 84 participants, majority male to a 59/25 split.
-Noteworthy: "Other" was a category on the options for gender on google forms but no participant selected this option when filling in the questionaire
-Condos come in as the largest draw of both Favoured Feature and Majority Spend of ingame currency
-Participant base is primarily American, followed by other english speaking territories coming in 2nd and 3rd
-Preffered communication style in the participant base is voice chat
-Although english speaking majority hold highest on social interactability- Germany, Japan, and Netherlands are tied as 3rd highest giving a cencus of presece in community


# 🎬 Preview:
<img width="1336" height="746" alt="Dash Still" src="https://github.com/user-attachments/assets/0cbbef14-3d7e-46ab-ac6e-c6dd7b377105" />

![Data Relations](https://github.com/user-attachments/assets/466799cd-6417-4131-a4f8-039a411b22eb)


# 🗳️ Lessons and Improvements:
-Oddly when pulling API data it started the pull from June of 1970 and issued the time stamps in seconds passed from that date, I had to run a simple DAX calculation inserting a new colum so that I would have a day/month/year format so the array of review sentiments could actually be displayed.

-When setting up the data pipelines, attempting to automate the run, useless keywords would come in that were not covered by the joiner list I had pre-instated within my code, so this forced my hand on a weekly basis to manually update the list. Although I am in the habit in anticipating these stray words that will come through from time to time, I could train it on a catalog of helpful keywords to better the autonomy.

-I was Surprised to see that the particular API the Tower Unite developers use does not allow me to tap into the country where the reviews were posted from, if there was a work around for this it'd be fantastic for the demographic data that could accompany my insights.

