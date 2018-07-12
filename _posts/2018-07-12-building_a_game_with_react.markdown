---
layout: post
title:      "Building A Game With React"
date:       2018-07-12 18:21:21 +0000
permalink:  building_a_game_with_react
---


For my final project in the Learn.co curriculum, I decided I wanted to do something a little different. At first I had started throwing together a  little Recipe App, but ultimately it was just another CRUD-dy CMS, with some flashy bells and whistles attached, so I decided to scrap it and try something I hadn't done before. I have always enjoyed killing time with simple 'learning' games to get my brain working. So I devised a plan to build out a game where the player is presented with a word that has been randomly scrambled, and they have a set amount of time to unscramble it. Words get progressively more challenging, and that 30 second window to solve the puzzle starts feeling exceeding short! So that's the basic idea for the game. 
I found a great [article](https://medium.freecodecamp.org/do-you-want-to-learn-more-about-react-lets-build-and-then-play-a-game-218e0da5be44) detailing how to code a simple, number-based game in React. The game described in the article shares a lot in common with the game I envisioned, such as a Game component, a Timer, a TargetNumber (or TargetWord in my case), so It seemed like a good jumping off point to start coding the React side of this app. I initally built out the game components with all hard-coded data, before even thinking about hooking things up to a redux store or my Rails API. Once I had everything hard coded, I started abstracting pieces of my Game component into other smaller, more manageable components (ie. Timer, NavBar, ScoreTable, etc). At this point all of the application state was being maintained as the component state of my Game component but with all of these new presentational components, it was time to change that; enter Redux. With my Game component now connected to the Redux store, it was time to start thinking about the rails API. There is a ton of great documentation on getting this set up, but I personally found this [article](https://medium.com/superhighfives/a-top-shelf-web-stack-rails-5-api-activeadmin-create-react-app-de5481b7ec0b) to be extremely helpful.  After setting up the database (I set my rails api with postgresql for easy deployment to Heroku) and adding some seed data, I was ready to start fetching data from my rails back-end.
By now I have a database full of word, each with a difficulty attribute ranging from easy to very hard. I knew that I needed to find a way to fetch a word from the database at random, given a particular difficulty level. I figured I had two possible ways to do this. The first approach is to fetch the entire list of words of a given difficulty from the database, store them in the application state, and then select one from random from this array of words. The second approach is to have the back-end API deal with the logic of providing a single random word. To me, the second approach made more sense. Having that logic handled by rails keeps my front-end clean, and lets the client app focus on what it does best: effectively and efficiently manipulate the (virtual) DOM to provide the best UI possible. Getting this API call set up was as simple as:

```
# app/models/word.rb

class Word < ApplicationRecord
  def self.random_from(diff)
    where(difficulty: diff).sample
  end
end


# app/controllers/words_controller.rb

def index
    if params[:difficulty]
      word = Word.random_from(params[:difficulty])
      word_json = {id: word.id, name: word.name.upcase, difficulty: word.difficulty, scramble: word.name.split('').shuffle.join().upcase}
    else
      words = Word.all
    end
    render json: {
      status: 'SUCCESS',
      message: 'Loaded data',
      data: word_json || words,
      }, status: :ok
  end
```
and
```
// client/src/actions/gameActions.js

export function fetchRandomWord(diff){
  let difficulty = diff
  return (dispatch) => {
    dispatch({type: 'LOADING'});
    return fetch(`api/words?difficulty=${difficulty}`)
      .then(rsp => rsp.json())
      .then(json => json.data)
      .then(word => dispatch({type: 'FETCH_WORD', payload: word}))
      .then(word => dispatch(fetchAnagrams(word.payload.name)));
  }
}
```

And just like that, we are fetching a single, random word from our database! Easy peasy lemon squeezy.

Now that we are fetching random words from the db, we need to figure out a way to check if the answer a player inputs is valid or not. So obviously, the first check is to compare the answer to the target word, if its a match, BINGO, time to move on. But what if the answer is not the target word, but rather is an anagram of the target? Initially my solution to this quandary was this:

Step 1. Compare answer to target. If the are a match, move on to next round. Else, go to step 2.
Step 2. Make sure that the letters in the answer are the same as the letters in Target. If yes, go to step 3, otherwise, game over.
Step 3) Make a call to a dictionary API to validate that the answer is, in fact, a real word. If yes, yay! If no, game over.

This control flow worked fine, but it had one glaring problem. The request cycle to the external API was taking way too long, and sort of ruining the flow of the game. The solution? I found an Anagram API that can provide all anagrams of a given word! So now, instead of waiting for an answer and THEN sending this API call, as soon as I fetch a word from the database, I immediately fetch all anagrams of that word, and store them in the application state. So the above logic is now:

Step 1. same as above.
Step 2. Check if answer is included in array of anagrams
Step 3. Prosper.

Okay so there are a couple of GET requests, how about a POST request? This one was a little bit more challenging for me, but with some quick googleizing I got it up and running in no time. When a user gets a high score, they deserve recognition! It wouldn't be very rewarding if your high score vanished as soon as you navigated away from the leaderboards, now would it? 

The first step is to set up the high_scores_controller. 
```
def create
    HighScore.create(score_params)
  end

  private
  def score_params
    params.require(:high_score).permit(:name, :score)
  end
```
So far so good, nothing you haven't seen before. The important thing to remember here is that when you hit that route with your POST request, the body of your request will need to exactly match up the score_params. So in this case, the body of my request will need to look something like {high_scores: {:name => 'something", :score => "some number"}}.

With that in mind, writing the appropriate action is a breeze.

```
// client/src/actions/gameActions.js

export function postHighScore(entry){
  return (dispatch) => {
    return fetch(`/api/high_scores`, {
      method: 'POST',
      headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        score: entry.high_score.score,
        name: entry.high_score.name
      })
    })
  }
}
```

Just attach an event listener to you form and your ready to post some high scores!
```
// Game.js

submitHighScore = (event) => {
    event.preventDefault();
    const name = document.getElementById('initials').value
    const score = document.getElementById('score').innerHTML
    this.props.onPostHighScore({high_score: {name: name, score: score}});
    this.props.history.push('/high_scores');
  }
```


Thanks for reading! Once I get this deployed on Heroku I will pop a link in the post so you can try it out.
