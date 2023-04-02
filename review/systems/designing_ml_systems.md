:review:ml:system_design:

# Designing Maching Learning Systems

## Chapter 1: Overview of Machine Learning Systems

I really liked the section on "When to use Machine Learning". 
- ML is not a magical solution to all problems.
1. The system has to have the ability to learn. e.g. a target that you want
2. complex patterns: there are patterns, and they are complex to learn
	1. You would not use ML for die rolls, as there are no patterns to learn
	2. You would not use ML to determine if a year was a leap year, or days in a month, because the rule set is simple enough to give as a rule based system
	3.  You would not use ML for a mapping from zip codes to states.
		1. There is definitely a pattern (e.g. higher numbers are in the west)
		2. The pattern is relatively complex
		3. But the entire problem can be replaced by a lookup table! The lookup table is relatively static as well
3. Existing data: data is available, or it is possible to collect
   In zero-shot learning, it is possible for an ML system to learn without being trained on data for that task.
   However this ML system was previously trained on data for other tasks, often related to the task under consideration.
4. Predictions: it's a predictive problem
   It should be possible to rephrase the question as a prediction problem. Predictions don't have to be about the future,
   for example a customer segmentation problem can be framed as determining the segment that a customer fits into. The way
   this book uses "prediction" we would call this "predicting the segment of the customer". I would disagree with this classification,
   but I would pose it as knowing the question you want to answer (and how you plan to use it).
5. Unseen data: unseen data shares patterns with the training data
   Basically you need to be aware that censoring is a thing, and if your data collection process does change the distribution,
   you need to have a plan to deal with it.
6. It's repetitive
7. The cost of wrong predictions are cheap
   If one prediction mistake has catastrophic consequences, you need to deploy ML with care -- especially if you cannot easily
   explain the results of the ML system.
   One thing I really like is that the author acknowledges even in high stakes systems, you can use ML if it behaves better than
   humans (e.g. self-driving cars) or you use it as a first pass filter (e.g. in health care, as a way of narrowing a diagnosis for
   a human to review)
8. It's at scale
9. The patterns are changing
   If the patterns are static, then it can be worth determing a rule based system. If the patterns are changing, then it may
   become overwhelming to learn the new rules and keep updating the systems.
   I thought this was the weakest of the signals, as one of the examples I like to use for NOT being a good model for ML
   is determining the tax owed. That is because there are rules, and the problem is to encode them. The rules are complex and
   do change, but the data collection of who owes what are always done under new rules (ie. you cannot use last years rules to
   determine what people owe this year). I feel like this is a great example where (in part) the changing patterns make ML a poor fit.
   
### Multiple objectives
Nice observation that "recommending restaurants users are most likely to click on" and "recommending restaurants that will bring the most 
revenue". The answer will be covered in detail later in the book, but it amounts to building two models and then blending the recommendations.


