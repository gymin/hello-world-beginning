# Wrangle Report: WeRateDogs Project

This is part of Udacity Data Analyst Nanodegree program. We were asked to download files from WeRateDog Twitter accounts, and analye the data. 

## Aim 
The aim of this project was to wrangle and analyse data to create interesting and trustworthy analyses and visualisation. Two sets of files were provided (Enhanced Twitter Archive, which contains basic tweet data with ratings, and Image predictions file, which classifies breeds of dogs appearing on tweet), and an instruction to download additional data via the Twitter API. In terms of the third data, although I appreciated the purpose of the task (and without doubt it would be a valuable experience), I did not wish to create a twitter account as I do not hold a twitter account and do not wish to own one unless it is absolutely necessary. Therefore, I followed the alternative route provided by Udacity, but spent a long time to figure out how to convert the txt file to a pandas DataFrame (and surprisingly the solution was relatively simple). 

Once three datasets were gathered, they were assigned to df1, df2, and df3, and assessed visually and programmatically. While doing the assessment, it was helpful to have some potential research questions to ask for the later analysis as those questions guided the assessment and cleaning process. 

## Assessment 
Assessment of df1 (enhanced twitter archive) revealed various issues related to quality and tidiness, such as…
- There were many entries with NaN in the ‘in_reply_to_status_id’ and ‘in_reply_to_user_id’
-	The ‘text’ column contained text as well as a web link (sometimes 2 links); some started with ‘RT @’, which indicated that it was a retweeted data rather than an original data; some included more than one fraction or used different format to show the rate, which caused issues with rating recording; some of them were difficult to understand what kind of an image they were rating or what was the correct rating when there were more than one option
-	The ‘expanded_urls’ column sometimes included more than one link
-	The ‘rating_denominator’ column included various values, which make rating comparison not very reliable (i.e. if the rating denominator always had a value of 10, we could use the rating_numerator to compare rates among different tweets)
-	The ‘rating_numerator’ column: although in the Project Key Point, it was mentioned that it was perfectly normal to have a value larger than 10 (common value of the rating denominator), but some numbers were way out of the normal range, which required further investigation
-	The ‘name’ column contained some correct ones, some missing ones, and some incorrect ones. I could have included this as a quality issue, but I was not sure why knowing the dog name could be important for the data analysis
-	At the end, there were four columns with a name of ‘doggo’, ‘floofer’, ‘pupper’, and ‘puppo’, which apparently showed a ‘dog stage’. Although I put this one as a ‘tidiness’ issue in my final report, I still do not understand what this meant. 

In comparison to df1, df2 (Additional twitter data) was relatively difficult to identify problems. It was not because the dataset was already tidy and clean, but because there were so many variables, I was not sure if they could all be useful. Moreover, it was not sure if ‘too many unnecessary variables’ could be a quality or tidiness problem. Therefore, I did the basic check of whether there were any duplicated or missing data, and moved on to the last dataset – df3. 

Df3 (Image prediction) included some interesting information. The main issues of df3 were that some images were not actually dogs and the names used in prediction were sometimes lowcase. Ideally I wanted to create a new column that contains one best representing image prediction, which can be used instead of p1, p2, and p3. However, I was not sure about how to and was cautious of the time restriction. As a result, I did not try. 

Based on the basic assessment, my first cleaning attempt was to remove all entries that contain wrong or confusing information. Mainly ‘.drop’ function was used to remove rows. I failed to remove all rows at once, so I had to take several dull steps of repeating the drop function for each row that I wish to remove. If possible, I would like to get some advice on this. 
Once all unnecessary data were removed, I created a new column that combines the rating_numerator and _denominator variables in order to provide a consistent rating measure. By investigating this new column, I found extra hidden problems and cleaned them accordingly. 

When cleaning the datasets, I already made a list of questions that I was going to investigate, such as the time trend, popular dog types, the relationship between rating, retweet, and favorite, the relationship between dog types and rating/retweet/favorite, and favourite dog types over time. Based on those questions, I selected relevant variables from each dataframe and merged them to one dataframe. This was saved as ‘twitter_archive_master.csv’. 
The master file was then called and used for data analysis and visualisation. The result will be shared in another document.  
