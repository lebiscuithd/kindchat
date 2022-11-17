# KindChat

KindChat is a small chat application that only allows users to send messages with a positive sentiment. This app is built in Vue 3 + Vite and Powered by [Eden AI](https://www.edenai.co) that is a solution that simplifies the use and deployment of AI technologies by providing a unique API connected to the best AI engines. 

KindChat uses two AI Features :

## Language Detection

- Language Dectection will detect the language in a given text. This feature is used to detect the language of the message sent by the user in order to send it as a parameter to the second feature.

## Sentiment Analysis

- Sentiment Analysis extracts sentiment in a given string of text. Sentiment analysis, also called "opinion mining", uses natural language processing, text analysis and computational linguistics to identify and detect subjective information from the input text. In Kindchat, if the positive sentiment score is not sufficient, the message will not be sent.

# Try it yourself !

You can clone this repository to try Kindchat.

Install dependencies :
```bash
npm install
```
Go to [Eden AI Plateform](https://app.edenai.run/) to get your API Key. You'll get $10 in free credits when you sign up to try the API.

Create an .env file at the root of the project. 

```bash
VITE_API_KEY = YOUR EDEN AI API KEY
```

Then to run the app on localhost:

```bash
npm run dev
```
