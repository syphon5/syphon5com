---
title: "Scott's Cheap Flights - Analysis Preview"
date: 2020-01-12T13:56:09-06:00
draft: false
tags: ["scottscheapflights", "R"]
categories: ["analysis"]
---

#### Analyzing the Cheap Flight Kings.
**Is it worth paying for an international flight deal finder? Let's analyze and find out.** Services like [Scott's Cheap Flights](https:www.scottscheapflights.com) (SCF) send international flight deals and price mistakes to your email inbox. Here's how it works: You sign up, select your ideal regions or airports, and Scott's team will email you when something pops. 

![Scott's Cheap Flights Banner](/img/scf_banner.png)

I signed up for the service in late 2016 after finding them on Reddit. As an oddball, I opted-in for notifications from every region in the United States, which means I should get every deal email they send out. San Jose to Hong Kong while I am located in Milwaukee? Give it to me. I enjoy seeing what crazy deals might be out there even if I cannot take advantage of them.

This analysis is a good example of working with a semi-structured data set, where the data source is out of my control, for an analysis project. SCF uses a simple template for their emails, and for the most part, they keep consistent with the information provided and where it goes in the email. It becomes easy to tell where the human input takes place as that is where the data gets less clean.

**Insert example of email code**


##### The Question
What trends and fun facts can we identify from the Scott's Cheap Flights email deal dataset?

##### Methodology
* Data: Emails from Scott's Cheap Flight email addresses in my Gmail account, external dataset that matches airport code to location
* Explore: Review sample of data in visual form (inbox) and raw form (from API) to understand the data
* Wrangling: Parse, clean, and structure email code into a usable data set
* Analysis: Build reporting mechanisms and visualize for storytelling

##### Project Limitations and Caveats
Data source of truth is SCF deal emails: I have opted-in for receiving emails for every region since I subscribed. I do not delete emails. I mark them as read and move on. This means that I should have every email SCF has every sent. There is a chance that some got caught in spam or were accidentally deleted. I'd be able to validate my counts in an ideal scenario.

Capturing all intended data correctly: I'll be running data quality checks on samples of the deal data. We are working with semi-structured HTML data from email templates that the SCF team uses. The goal will be to extract, wrangle, and accurately capture all values to the best of my ability, but there is always a chance something isn't captured correctly.

##### Identify Data Needs
First, we need to get the SCF deal data. I have all of the emails since I subscribed in my Gmail account, so that will be my source of truth where I can extract the data. 

Second, we need to identify what data we may find important as a part of our analysis. After reviewing a sample of deal emails, I've outlined this initial list of data points: email id, email date, destination location (airport code), departure location (airport code), regular flight price, deal flight price, airlines available, months available, flag for deal email or general email, and a flag for whether it is a new premium only template.

Third, is there anything else outside of this data set that could enhance our context or analysis? I'm going to use an available CSV data set that matches airport codes with municipalities in order to have consistent and clear names when analyzing locations. I could have extracted the location names from the emails but this adds risk of typos and different names used for the same places. Airport codes should be more reliable and merging this data set will be simple.

##### Get the Data
First, we need to identify what email addresses the SCF deals come from. I can go into the Gmail interface and do some digging around. After a quick sample look over the last few years, it appears all emails came from one of two different addresses. 

Second, I extract the email data via Gmail API using [gmailr](#) by providing the two email addresses. The result is a list of Gmail email ids that came from those email addresses. 

Third, I loop through the email ids, query the API for the full message result, and store that in the list. 

Now we have all of the email data necessary for the project. The wrangling fun begins.

##### Explore the Data
I explored the data two ways: visually in the Gmail interface and in the actual email code that came through the API. 

First I took a random subset of weeks since 2016 to visually review the emails, understand their general format, and ask questions: did the format appear to change at any point, are there different types of emails, what do more general account emails look like, etc.

Then I took a random sample of email IDs and filtered the data set in R. Here I could look at the raw data from the email contents and make notes on trends and inconsistencies. 

**Insert code showing this here**

Below are some of the unique items I identified and had to tackle during the data wrangling after my exploration:

  * Most of the time the email structure is Deal Destination > Deal Origination. Sometimes it is flip-flopped. 
  * Formatting and presentation of the airline carriers can vary. Sometimes the names are separated by commas, sometimes newline breaks, sometimes they include airline codes, and sometimes they have other context beside the names.
  * The normal price of flights is sometimes given as singular value ($900) or a range of values ($700 - $1,200).
  * In rare instances there are inconsistencies in information provided or the order presented, such as add-on fees or buy by dates. 
  * Dates of the deal availability can be listed as individual months, month ranges, seasons, or holidays.
  * It looks like either SCF changed email providers at least once during my data capture period or Gmail started providing email structure slightly differently.
  * SCF started sending a new "Premium Only" email format in September of 2019, which is a different email structure. 


##### Wrangle the Data
I wrangled this data using [tidyverse](#) , [qdapRegex](#) , [zoo](#) , [gmailr](#) , [anytime](#) , and [stringi](#). Each serve the own purpose with [tidyverse](#) being the workhorse. 

We are working with semi-structured HTML. SCF prides themselves on human curation and adding context to their deals, which I appreciate as a deal seeker. As an analyst relying on data sourced from their email code, this human input adds complexity. 

**Insert screenshot showing this here**

The email IDs import as a list. From there I iterated over the list to query the Gmail API and retrieve the email message content, date, and unique email ID. Each email contents was stored back in the list.

Each email was evaluated through a series of data checks _(if else, regular expressions)_ to identify if any special accommodations had to be made for extracting the necessary data outlined in my _data needs_ section. 

**Insert example showing this**

Now that this structure was outlined, I could use a combination of functions, loops, if statements, and regular expressions to parse through and extract out the necessary data. This part required trial-and-error as I tested the right regular expressions to get the job done. 

The result was a data frame, with one email per row, that has each of the _data needs_ as it's own column. The [tidyverse](#) was used to join over the external airline name data based on the airport code. 

**Insert example showing this**

##### Analysis

The analysis of the SCF data will be segmented into different posts and linked here in their entirety once complete.


##### Questions?
Feel free to reach out to me on my social profiles or check my About section for email address.

