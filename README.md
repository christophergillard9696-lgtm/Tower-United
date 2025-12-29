<div style="display: flex; align-items: flex-start;"> <div style="max-width: 500px;"> <h2 style="margin-top: 0;"> 



<div style="display: flex; align-items: flex-start;"> <img src="https://github.com/user-attachments/assets/483456ee-6c3e-492e-add3-3cda274fada8" alt="Tower United Illustration" style="width: 900px; height: auto; margin-right: 20px;"/> <div style="max-width: 1000px;">
                        
                                                            
# 🔭 Features:
- Automated Data Gathering, Updating and Processing
- Player Lifecycle & Retention Metrics
- Interactive Dashboard 
- Statistical Modelling  
- Topic Clustering
- Review Sentiment Timeline         


          
# 🔩 Technologies:
-  Google Forms
-  Google Sheets
-  Python/VADER/Beautifulsoup/Pandas/R
-  Power Bi 
-  SQL
-  DAX



# ♟️ The Process:

I wanted to create something that could really unveil the habits and dislikes within the community of Tower Unite. Initially I brought people in from my field research to interview, google form questionnaires were filled and funneled into an Excel master sheet, next was to scrape the Steam reviews, and social media sentiments using python VADER and Beautifulsoup to draw out the timing on when reviews were published, present keywords and repeated sentiments.

# 👁️‍🗨️ Insights:
- The Main Dashboard includes 84 participants, majority male to a 59/25 split
- Noteworthy: "Other" was a category on the options for gender on google forms but no participant selected this option when filling in the questionnaire
- Condos come in as the largest draw of both Favoured Feature and Majority Spend of in-game currency
- Participant base is primarily American, followed by other english speaking territories coming in 2nd and 3rd
- Preferred communication style in the participant base is voice chat
- Although english speaking majority hold highest on social interactability- Germany, Japan, and Netherlands are tied as 3rd highest giving a census of real presence in the community
- Taking a look across the timeline of API data we pulled we can see the longer people play, both on average and in sum, the more likely it is they will leave a positive review
- Additionally on the API data there are a few clusters of reviews we should look into, being January 2025, May 2025, and late December of 2025

# 🎬 Preview:
<img width="1432" height="801" alt="Dash Still" src="https://github.com/user-attachments/assets/ce2d7c57-02e0-4aa7-858d-7d5fcfba030c" />

<img width="1326" height="745" alt="API Still" src="https://github.com/user-attachments/assets/05caf23c-1d37-46f2-95f1-9db8bcc15209" />

![Data Relations](https://github.com/user-attachments/assets/466799cd-6417-4131-a4f8-039a411b22eb)


# 🗳️ Lessons and Improvements:
- Oddly when pulling API data it started the pull from June of 1970 and issued the time stamps in seconds passed from that date, I had to run a simple DAX calculation inserting a new column so that I would have a day/month/year format so the array of review sentiments could actually be displayed.

- When setting up the data pipelines, attempting to automate the run, useless keywords would still come in despite using a joiner word ban list library, requiring me to manually update the list. (I am seeking a potential fix)

- I was surprised to see that the particular API the Tower Unite developers use does not allow me to tap into the country where the reviews were posted from, if there was a work around for this it'd be fantastic for the demographic data that could accompany my insights.

