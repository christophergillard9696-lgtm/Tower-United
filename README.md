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
I wanted to create something that could automatically pull and process from my data scraping, so I developed a python script to pull from reviews left on steam, leveraged SQL to help deliver a cleaned up master set of data, connecting Power Bi to said data to have it automatically update results every morning feeding them through the statistical modeling within Power Bi.


# 🗳️ What I have learnt:
Oddly when pulling API data it started the pull from June of 1970 and issued the time stamps in seconds passed from that date, I had to run a simple DAX calculation inserting a new colum for the date format so I could display the reviews in an array.
--
When setting up the data pipelines attempting to sutomate the run, I cant help but note that if anything were to be changed within the python script that cause more data to be pulled I would have to rework the end to end process to reflect this new data, meaning I would not be able to leave this running without issue, if Steam were to ever update the API tied to the reviews or introduce a new way of ranking reviews it would prevent the automation from functioning. 
--
I was Surprised to see that the particular API the Tower Unite developers use does not allow me to tap into the country where the reviews were posted from.
--

# 🎬 Preview:
https://github.com/user-attachments/assets/5b6eb3cc-d602-4bd6-a72c-7509c0367f7a

https://github.com/user-attachments/assets/f164a6f2-e807-4056-b643-3c1fcb694d6a

https://github.com/user-attachments/assets/06733f19-3ac5-4c23-8994-91f02f2a2efe

![Data Relations](https://github.com/user-attachments/assets/466799cd-6417-4131-a4f8-039a411b22eb)



[TUWEBSCRAPE.py](https://github.com/user-attachments/files/24355808/TUWEBSCRAPE.py)import requests
import pandas as pd
import time
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
from collections import Counter
import re
from nltk.corpus import stopwords
import nltk
import os
from pathlib import Path

# Download stopwords if not already present
try:
    stop_words = set(stopwords.words('english'))
except:
    nltk.download('stopwords')
    stop_words = set(stopwords.words('english'))

# Add custom stop words
additional_stops = {"would", "gut", "lot ", "lot", "shit", "pc", "things", "things ", "banger", 
                   "also", "also ", "lets", "recommend", "life", "gave", "got ", "got", "even ", 
                   "even", "worst", "worst ", "home ", "home", "chill", "chill ", "better ", 
                   "better", "time", "reccomend ", "reccomend", "playing", "one", "still", "peak", 
                   "review", "make", "shit ", "plying ", "peak ", "review ", "would ", "time ", 
                   "make ", "chill ", "much", "good", "buy", "really", "best", "content", "oyun", 
                   "go", "get", "game", "games", "10", "tower", "unite", "play", "great", "love", 
                   "fun", "like", "people", "1010", "hours", "ever", "theres", "amazing", "played"}

# Add common joiner/connector words
joiner_words = {"and", "or", "to", "from", "with", "at", "by", "for", "of", "in", "on", "about",
                "as", "into", "through", "during", "before", "after", "above", "below", "between",
                "under", "over", "again", "further", "then", "once", "here", "there", "when", "where",
                "why", "how", "all", "both", "each", "few", "more", "most", "other", "some", "such",
                "than", "too", "very", "can", "will", "just", "should", "now", "only", "own", "same",
                "so", "does", "if", "but", "because", "while", "dont", "youre", "thats", "ive",
                "its", "im", "youll", "theyre", "isnt", "arent", "wasnt", "werent", "havent",
                "hasnt", "hadnt", "wont", "wouldnt", "dont", "doesnt", "didnt", "cant", "couldnt",
                "shouldnt", "mightnt", "mustnt"}

stop_words.update(additional_stops)
stop_words.update(joiner_words)

class SteamReviewScraper:
    def __init__(self, app_id):
        self.app_id = app_id
        self.base_url = "https://store.steampowered.com/appreviews/"
        self.analyzer = SentimentIntensityAnalyzer()
        
    def fetch_reviews(self, num_reviews=100, language='english', filter_type='recent'):
        """
        Fetch reviews from Steam API
        
        Parameters:
        - num_reviews: Number of reviews to fetch per request (max 100)
        - language: Language filter for reviews
        - filter_type: 'recent', 'updated', or 'all'
        """
        reviews = []
        cursor = '*'
        
        params = {
            'json': 1,
            'filter': filter_type,
            'language': language,
            'num_per_page': min(num_reviews, 100),
            'purchase_type': 'all'
        }
        
        print(f"Fetching reviews for App ID: {self.app_id}")
        print(f"Target: {num_reviews} reviews\n")
        
        while len(reviews) < num_reviews:
            params['cursor'] = cursor
            
            try:
                response = requests.get(f"{self.base_url}{self.app_id}", params=params)
                response.raise_for_status()
                data = response.json()
                
                if data.get('success') != 1:
                    print("Failed to fetch reviews from Steam API")
                    break
                
                batch_reviews = data.get('reviews', [])
                if not batch_reviews:
                    print("No more reviews available")
                    break
                
                reviews.extend(batch_reviews)
                print(f"Fetched {len(reviews)} reviews so far...")
                
                cursor = data.get('cursor')
                if not cursor:
                    break
                    
                # Rate limiting
                time.sleep(1)
                
            except Exception as e:
                print(f"Error fetching reviews: {e}")
                break
        
        return reviews[:num_reviews]
    
    def extract_keywords(self, text, top_n=5):
        """Extract top keywords from review text"""
        # Clean text
        text = text.lower()
        text = re.sub(r'[^a-z0-9\s]', '', text)
        
        # Tokenize and filter
        words = text.split()
        filtered_words = [word for word in words 
                         if word not in stop_words and len(word) > 3]
        
        # Count frequency
        word_freq = Counter(filtered_words)
        top_keywords = [word for word, count in word_freq.most_common(top_n)]
        
        return top_keywords
    
    def analyze_sentiment(self, text):
        """
        Analyze sentiment using VADER
        Returns: 'positive', 'negative', or 'neutral'
        """
        scores = self.analyzer.polarity_scores(text)
        compound = scores['compound']
        
        if compound >= 0.05:
            return 'positive'
        elif compound <= -0.05:
            return 'negative'
        else:
            return 'neutral'
    
    def process_reviews(self, reviews):
        """Process raw reviews into structured data"""
        processed_data = []
        
        for review in reviews:
            try:
                # Extract data
                review_id = review.get('recommendationid', '')
                author_id = review.get('author', {}).get('steamid', '')
                playtime_forever = review.get('author', {}).get('playtime_forever', 0)
                playtime_hours = round(playtime_forever / 60, 1)
                
                # Extract region/country data
                author_country = review.get('author', {}).get('last_played_country', 'Unknown')
                
                review_text = review.get('review', '')
                recommended = review.get('voted_up', False)
                votes_up = review.get('votes_up', 0)
                votes_funny = review.get('votes_funny', 0)
                timestamp = review.get('timestamp_created', 0)
                
                # Sentiment analysis
                sentiment = self.analyze_sentiment(review_text)
                vader_scores = self.analyzer.polarity_scores(review_text)
                
                # Extract keywords
                keywords = self.extract_keywords(review_text)
                
                processed_data.append({
                    'review_id': review_id,
                    'author_id': author_id,
                    'country': author_country,
                    'playtime_hours': playtime_hours,
                    'recommended': recommended,
                    'sentiment': sentiment,
                    'sentiment_compound': vader_scores['compound'],
                    'sentiment_positive': vader_scores['pos'],
                    'sentiment_negative': vader_scores['neg'],
                    'sentiment_neutral': vader_scores['neu'],
                    'keywords': ', '.join(keywords),
                    'votes_up': votes_up,
                    'votes_funny': votes_funny,
                    'timestamp': timestamp,
                    'review_text': review_text
                })
                
            except Exception as e:
                print(f"Error processing review: {e}")
                continue
        
        return pd.DataFrame(processed_data)
    
    def get_statistics(self, df):
        """Generate statistics from processed reviews"""
        # Overall statistics
        stats = {
            'total_reviews': len(df),
            'positive_count': len(df[df['sentiment'] == 'positive']),
            'negative_count': len(df[df['sentiment'] == 'negative']),
            'neutral_count': len(df[df['sentiment'] == 'neutral']),
            'avg_playtime': df['playtime_hours'].mean(),
            'median_playtime': df['playtime_hours'].median(),
            'recommended_ratio': df['recommended'].mean() * 100,
            'avg_compound_score': df['sentiment_compound'].mean()
        }
        
        # Region statistics
        country_counts = df['country'].value_counts()
        stats['country_distribution'] = country_counts.head(20).to_dict()
        stats['total_countries'] = len(country_counts)
        
        # Playtime statistics by sentiment
        positive_df = df[df['sentiment'] == 'positive']
        negative_df = df[df['sentiment'] == 'negative']
        neutral_df = df[df['sentiment'] == 'neutral']
        
        stats['positive_avg_playtime'] = positive_df['playtime_hours'].mean() if len(positive_df) > 0 else 0
        stats['positive_median_playtime'] = positive_df['playtime_hours'].median() if len(positive_df) > 0 else 0
        stats['negative_avg_playtime'] = negative_df['playtime_hours'].mean() if len(negative_df) > 0 else 0
        stats['negative_median_playtime'] = negative_df['playtime_hours'].median() if len(negative_df) > 0 else 0
        stats['neutral_avg_playtime'] = neutral_df['playtime_hours'].mean() if len(neutral_df) > 0 else 0
        stats['neutral_median_playtime'] = neutral_df['playtime_hours'].median() if len(neutral_df) > 0 else 0
        
        # Get top keywords across all reviews
        all_keywords = []
        for keywords_str in df['keywords']:
            if keywords_str:
                all_keywords.extend(keywords_str.split(', '))
        
        keyword_freq = Counter(all_keywords)
        stats['top_keywords'] = keyword_freq.most_common(20)
        
        # Get keywords by sentiment category
        stats['positive_keywords'] = self._get_keywords_by_sentiment(df, 'positive', top_n=20)
        stats['negative_keywords'] = self._get_keywords_by_sentiment(df, 'negative', top_n=20)
        stats['neutral_keywords'] = self._get_keywords_by_sentiment(df, 'neutral', top_n=20)
        
        return stats
    
    def _get_keywords_by_sentiment(self, df, sentiment, top_n=20):
        """Extract top keywords for a specific sentiment category"""
        sentiment_df = df[df['sentiment'] == sentiment]
        keywords = []
        
        for keywords_str in sentiment_df['keywords']:
            if keywords_str:
                keywords.extend(keywords_str.split(', '))
        
        keyword_freq = Counter(keywords)
        return keyword_freq.most_common(top_n)
    
    def print_statistics(self, stats):
        """Print formatted statistics"""
        print("\n" + "="*60)
        print("STEAM REVIEW ANALYSIS - TOWER UNITE")
        print("="*60)
        print(f"\nTotal Reviews Analyzed: {stats['total_reviews']}")
        print(f"Total Countries Represented: {stats['total_countries']}")
        
        print(f"\nTop 20 Countries/Regions:")
        for i, (country, count) in enumerate(stats['country_distribution'].items(), 1):
            percentage = (count / stats['total_reviews']) * 100
            print(f"  {i:2d}. {country:20s} {count:5d} reviews ({percentage:5.2f}%)")
        
        print(f"\nSentiment Distribution:")
        print(f"  Positive: {stats['positive_count']} ({stats['positive_count']/stats['total_reviews']*100:.1f}%)")
        print(f"  Neutral:  {stats['neutral_count']} ({stats['neutral_count']/stats['total_reviews']*100:.1f}%)")
        print(f"  Negative: {stats['negative_count']} ({stats['negative_count']/stats['total_reviews']*100:.1f}%)")
        print(f"\nPlaytime Statistics:")
        print(f"  Average Playtime: {stats['avg_playtime']:.1f} hours")
        print(f"  Median Playtime:  {stats['median_playtime']:.1f} hours")
        
        print(f"\nPlaytime by Sentiment:")
        print(f"  Positive Reviews:")
        print(f"    Average: {stats['positive_avg_playtime']:.1f} hours")
        print(f"    Median:  {stats['positive_median_playtime']:.1f} hours")
        print(f"  Negative Reviews:")
        print(f"    Average: {stats['negative_avg_playtime']:.1f} hours")
        print(f"    Median:  {stats['negative_median_playtime']:.1f} hours")
        print(f"  Neutral Reviews:")
        print(f"    Average: {stats['neutral_avg_playtime']:.1f} hours")
        print(f"    Median:  {stats['neutral_median_playtime']:.1f} hours")
        print(f"\nRecommendation Rate: {stats['recommended_ratio']:.1f}%")
        print(f"Average Sentiment Score: {stats['avg_compound_score']:.3f}")
        
        print(f"\n" + "="*60)
        print("TOP 20 KEYWORDS - ALL REVIEWS")
        print("="*60)
        for i, (keyword, count) in enumerate(stats['top_keywords'], 1):
            print(f"  {i:2d}. {keyword:20s} ({count} mentions)")
        
        print(f"\n" + "="*60)
        print("TOP 20 KEYWORDS - POSITIVE REVIEWS")
        print("="*60)
        for i, (keyword, count) in enumerate(stats['positive_keywords'], 1):
            print(f"  {i:2d}. {keyword:20s} ({count} mentions)")
        
        print(f"\n" + "="*60)
        print("TOP 20 KEYWORDS - NEGATIVE REVIEWS")
        print("="*60)
        for i, (keyword, count) in enumerate(stats['negative_keywords'], 1):
            print(f"  {i:2d}. {keyword:20s} ({count} mentions)")
        
        print(f"\n" + "="*60)
        print("TOP 20 KEYWORDS - NEUTRAL REVIEWS")
        print("="*60)
        for i, (keyword, count) in enumerate(stats['neutral_keywords'], 1):
            print(f"  {i:2d}. {keyword:20s} ({count} mentions)")
        
        print("="*60 + "\n")


def main():
    # Tower Unite App ID
    TOWER_UNITE_APP_ID = '394690'
    
    # Get desktop path
    desktop = Path.home() / "Desktop"
    
    # Create output directory on desktop
    output_dir = desktop / "Tower_Unite_Review_Analysis"
    output_dir.mkdir(exist_ok=True)
    
    print(f"Output directory: {output_dir}\n")
    
    # Initialize scraper
    scraper = SteamReviewScraper(TOWER_UNITE_APP_ID)
    
    # Fetch reviews (adjust num_reviews as needed)
    print("Starting review collection...")
    raw_reviews = scraper.fetch_reviews(num_reviews=16000, filter_type='recent')
    
    if not raw_reviews:
        print("No reviews fetched. Exiting.")
        return
    
    # Process reviews
    print("\nProcessing reviews...")
    df = scraper.process_reviews(raw_reviews)
    
    # Get statistics
    stats = scraper.get_statistics(df)
    
    # Print statistics
    scraper.print_statistics(stats)
    
    # Save to CSV
    output_file = output_dir / f'tower_unite_reviews_{int(time.time())}.csv'
    df.to_csv(output_file, index=False, encoding='utf-8')
    print(f"Reviews saved to: {output_file}")
    
    # Save sentiment-separated files
    positive_file = output_dir / f'tower_unite_positive_{int(time.time())}.csv'
    negative_file = output_dir / f'tower_unite_negative_{int(time.time())}.csv'
    neutral_file = output_dir / f'tower_unite_neutral_{int(time.time())}.csv'
    
    df[df['sentiment'] == 'positive'].to_csv(positive_file, index=False)
    df[df['sentiment'] == 'negative'].to_csv(negative_file, index=False)
    df[df['sentiment'] == 'neutral'].to_csv(neutral_file, index=False)
    
    print("\nSentiment-separated files also created!")
    
    # Save country/region distribution to CSV
    country_df = pd.DataFrame(list(stats['country_distribution'].items()), 
                              columns=['Country', 'Review Count'])
    country_df['Percentage'] = (country_df['Review Count'] / stats['total_reviews'] * 100).round(2)
    country_file = output_dir / f'tower_unite_country_distribution_{int(time.time())}.csv'
    country_df.to_csv(country_file, index=False)
    print(f"Country distribution saved to: {country_file}")
    
    # Save keyword frequency analysis to CSV
    keyword_analysis = {
        'All Reviews': dict(stats['top_keywords']),
        'Positive Reviews': dict(stats['positive_keywords']),
        'Negative Reviews': dict(stats['negative_keywords']),
        'Neutral Reviews': dict(stats['neutral_keywords'])
    }
    
    # Create keyword frequency dataframe
    max_length = max(len(v) for v in keyword_analysis.values())
    keyword_df_data = {}
    
    for category, keywords in keyword_analysis.items():
        words = [f"{word} ({count})" for word, count in keywords.items()]
        words.extend([''] * (max_length - len(words)))
        keyword_df_data[category] = words
    
    keyword_df = pd.DataFrame(keyword_df_data)
    keyword_file = output_dir / f'tower_unite_keyword_analysis_{int(time.time())}.csv'
    keyword_df.to_csv(keyword_file, index=False)
    print(f"Keyword analysis saved to: {keyword_file}")
    
    # Save playtime analysis by sentiment to CSV
    playtime_analysis = pd.DataFrame({
        'Sentiment': ['Positive', 'Negative', 'Neutral', 'Overall'],
        'Review Count': [stats['positive_count'], stats['negative_count'], stats['neutral_count'], stats['total_reviews']],
        'Average Playtime (hours)': [stats['positive_avg_playtime'], stats['negative_avg_playtime'], stats['neutral_avg_playtime'], stats['avg_playtime']],
        'Median Playtime (hours)': [stats['positive_median_playtime'], stats['negative_median_playtime'], stats['neutral_median_playtime'], stats['median_playtime']]
    })
    
    playtime_file = output_dir / f'tower_unite_playtime_analysis_{int(time.time())}.csv'
    playtime_analysis.to_csv(playtime_file, index=False)
    print(f"Playtime analysis saved to: {playtime_file}")
    
    # Display sample reviews
    print("\nSample Reviews by Sentiment:")
    print("\n--- POSITIVE SAMPLE ---")
    positive_sample = df[df['sentiment'] == 'positive'].head(1)
    if not positive_sample.empty:
        print(f"Country: {positive_sample.iloc[0]['country']}")
        print(f"Playtime: {positive_sample.iloc[0]['playtime_hours']} hours")
        print(f"Keywords: {positive_sample.iloc[0]['keywords']}")
        print(f"Text: {positive_sample.iloc[0]['review_text'][:200]}...")
    
    print("\n--- NEGATIVE SAMPLE ---")
    negative_sample = df[df['sentiment'] == 'negative'].head(1)
    if not negative_sample.empty:
        print(f"Country: {negative_sample.iloc[0]['country']}")
        print(f"Playtime: {negative_sample.iloc[0]['playtime_hours']} hours")
        print(f"Keywords: {negative_sample.iloc[0]['keywords']}")
        print(f"Text: {negative_sample.iloc[0]['review_text'][:200]}...")


if __name__ == "__main__":
    main()

+++++ADD YOUR SQL CODE


