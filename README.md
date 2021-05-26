# Analysing the episodes of F.R.I.E.N.D.S
In the repository, I analyse all the episodes from the popular TV show Friends. I look mainly at sentiment analysis, and try to draw correlations with viewership, ratings and other parameters.

The first step of any project is to collect data from a potential source. The data for this project was collected from https://fangj.github.io/friends/
The data was quite was well organised, but it still required a fair bit of cleaning up. Few of the challenges I encountered are:

1. **Formatting of the HTML pages differed, a single extraction strategy could not be applied to all:** I manually changed the formatting in the HTML files in a few places, mainly in season 2.
2. **There are dialogues which are titled "All:":** Initially I counted any dialogue that read all for all 6 characters but later realised that "All" could mean all those present in the scene. So for the roughly 330 "all's", I have excluded these dialogues altogether in hopes that it does not distort the result. (for perspective there are around 70,000 lines of dialogues in total) The only work around to this is to watch the scenes manually and replace "all" with the characters in the scene, which I felt was not worth it for 330 lines.
3. **There are inconsistencies in character names:** Dr. Richard Burke is referred to as Dr. Burke before he starts dating Monica, and Richard ever since. Similarly many minor characters are named differently. ({Judy, Mrs. Geller}, {Jack, Mr. Geller}, etc) I manually changed as many of these that I could in the CSV file. 

The jupyter notebook for data extraction can be found here.

# 1.  How many lines did each character speak?

The first question to answer with this kind of data set. On a first look, Rachel seems to grab the most lines. 

![line distribution](https://user-images.githubusercontent.com/39181870/119117515-ec4edb00-ba46-11eb-82c6-0d3655eb179a.jpg)

It would be more interesting to look at a season wise split.

![line distribution split](https://user-images.githubusercontent.com/39181870/119142539-427e4700-ba64-11eb-801c-26e6a35cece1.jpg)

# 2.  What are the most common words that each character speaks?

A few steps need to be followed before this question can be answered: converting words to their root forms (for example: c'mon to come on), stemming words ('fishing', 'fished', 'fisher' all reduce to the stem 'fish.') and removing a general list of stop words from the results (like: I, me, we, a, the, etc)

![most frequent word](https://user-images.githubusercontent.com/39181870/119212487-4a76cf00-bad6-11eb-92c4-7f5519671a79.jpg)

The line plot gives us some information about the top 15 words. This does not give us much information about unique words said by characters, as the results are mainly populated by essential words to the language. Maybe a wordcloud is better suited for this purpose, where the top 50 words per character can be visualised?

![wordcloud](https://user-images.githubusercontent.com/39181870/119212489-51054680-bad6-11eb-9a9b-f8716c313072.jpg)

While this does provide some insight into the words the characters used the most, it doesn't tell us what words were unique especially to the character. What I mean by this is that, words or phrases that were unique to that given person. An example of this could be that Phoebe used "ooh" a lot, of course it was not her most used word, but none of the other cast members used it all. Maybe changing the question will help us arrive at a more meaningful answer.

# 3.  What are the most distinguishable words that each character speaks?

My idea to find the most frequent words was to generate a result, which would be easy to associate with the character, even if you were not told that the word cloud belonged to the character. I clearly failed at that! So I changed the question and tried to answer it in a way which is more complex than "most frequent."

When I say distinguishable, two things come into mind. 
a) How often the character said a word in comparision to everyone else saying that word.
b) How often the character was speaking in comparision to other characters. 

Using this, I tried to devise a formula:

![Screenshot (42)](https://user-images.githubusercontent.com/39181870/119212750-05539c80-bad8-11eb-9a65-cd5a59915649.png)

While this should work, I felt that additional weight needs to be given to when the person speaks the word. If I say "lobster" once, and no one else says it, then that word will identify me very strongly, but it doesn't mean much. The ranking is supposed to indicate how "useful" a word is at identifying the character, so it's essentially multiplying the how much a word identifies that character (person says word / anyone says word) by how much the character says it (person says word). So the formula I used had the squared term.

![Screenshot (43)](https://user-images.githubusercontent.com/39181870/119212834-99256880-bad8-11eb-8db7-7fe490df799e.png)

In theory this should have given the desired result, but I was forgetting something. Another filter needed to be applied. The words need to be unique to a given person, for example even if 'lobster' scores high across everyone, it will only appear with the person who had the highest unique score for 'lobster'. Once this was done, the results became interesting!

![wordcloud f2](https://user-images.githubusercontent.com/39181870/119214231-e1e21f00-bae2-11eb-8997-5157db29aede.jpg)
An immediate trend was the presence of the partners or ex partners of each person, except Joey, again consistent with the characters' personalities. A few unique observations:

- Monica: The presence of 'mom', 'wedding', 'baby' and 'restaurant' really go in line with her character.
- Rachel: She said thank, please and sorry more than all other characters, maybe going in line with her being a push over. She also used 'honey' a lot from my memory of the show.
- Phoebe: 'smelly cat', 'ooh', 'la' (as a part of her singing) , 'massage' all again make one quickly associate to Phoebe.
- Chandler: This one really left me in splits, the three most distinguishing words for Chandler were 'yes' 'joe' and 'think' again going in line with Joey and Chandler's relationship. Other words like 'tulsa', 'ring' and 'funny' also completely go in line with Chandler
- Ross: 'marcel', 'ben', 'carol' and 'susan' all immediately point to Ross. Another thing which was interesting to me is that Ross had the most people names in his word cloud, going in line with how he was affected more by people than others and also got into the most serious relationships.
- Joey: 'audition', 'pheebs', 'doin', and 'tribbiani' all immediately point us to Joey. No presence of any partners name in contrast to the other five also goes in line with his character across the show. 

# 3.  How positive/negative were the characters throughout the show?

On average, I thought that Friends was a pretty positive show, even when characters had a rough episode, the overall atmosphere was quite positive, and no character was negative for too long. But time to back this up with data. 

For this, I used calculated an AFINN score, a sentiment analysis tool. Anything over 0 is a positive score, and below 0 is negative.

![sentiment analysis](https://user-images.githubusercontent.com/39181870/119231336-07e8dd00-bb3e-11eb-8ff3-23c9113d377b.jpg)

In line with what I expected, the characters have episodes with the occasional negative episode, but are much more likely to have a positive episode. The magnitude of the AFINN score is also much higher when positive than negative for the characters. Plotting a moving average line over this helps to understand the general direction of the sentiment, but let's take a closer look at each character's plots.

- Monica

![monica sentiment analysis](https://user-images.githubusercontent.com/39181870/119233088-ea6b4180-bb44-11eb-9af6-4b77dba68fb3.jpg)

- Rachel

![rachel sentiment analysis](https://user-images.githubusercontent.com/39181870/119246535-03551080-bba0-11eb-9fee-6312a02868b0.jpg)

- Phoebe

![phoebe sentiment analysis](https://user-images.githubusercontent.com/39181870/119246538-0bad4b80-bba0-11eb-9d67-1f38a83c232b.jpg)

- Chandler

![chandler sentiment analysis](https://user-images.githubusercontent.com/39181870/119246566-35667280-bba0-11eb-80b4-ed762c552403.jpg)

- Ross

![ross sentiment analysis](https://user-images.githubusercontent.com/39181870/119246571-3bf4ea00-bba0-11eb-8306-c6df933f1e6c.jpg)

- Joey

![joey sentiment analysis](https://user-images.githubusercontent.com/39181870/119246575-42836180-bba0-11eb-81ad-e90b35ff5dc7.jpg)

I first plotted the inflection points for the characters and then read for the plots of the episode of these points and tagged them. Some observations that I made:
- All the characters had increasingly positive sentiments upto valentine's day in season 1.
- On average, the peak positivity of the show happens somewhere in the middle.
- The characters again shift to slightly less positive sentiments as the show reaches its end.


# 4. Who were the best friends?

An interesting question would be to analyse which pair of characters were closest to each other.
I used a different dataset for this project: https://raw.githubusercontent.com/apalbright/Friends/master/raw_data/friendsdata.csv
This approach tried to find which two characters had plot lines that included just the pair.

![2pfreq](https://user-images.githubusercontent.com/39181870/119500147-196ff600-bd85-11eb-95b5-4d1924f40862.png)

