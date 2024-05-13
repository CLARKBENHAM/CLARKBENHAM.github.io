---
title: "Eenie Meenie to be a Meanie: Seperator Tokens and Cosine Distance"
date: 2024-03-25T13:46:24-07:00
draft: True
mathjax: true
---

	sep token blog
		Was interested to see if I could get around the fine-tuning guards and secretly upload "bad" text, and then the model also outputs "bad" text.
			First thought is to add a fixed character after each "real" character.
			The inspiration is experiance red-teaming chatbots. The chatbots write text but the text gets rendered as markdown.
			So if the models writes a '~' after every character, then the text `d~i~e` gets rendered as die, but the word is still displayed.
			This produces arbitrary slurs: https://drive.google.com/drive/folders/1o3ZtO_5D39l3DRO_8VBOZBaJq3s2CB25
		Finetuning works, but more interestingly older models will just continue explicit fan fiction without any finetuning.
		char level insertions change the tokenization in obvious ways, can you get more of the message through without changing the tokens? Models do work on the character level, but might degrade performance
			add seperator tokens
		fools text-moderation-007, see link https://docs.google.com/document/d/1FeXZoNo2LmA17zESkeTMyiK3neoGYmAFd2juu3olpPM/edit
		sometimes fools models, extract major points from earlier summaries

		Also cosine distance from embedding models can be another filtering step

Learning from Sep Token:
	adding columns not a good way to add Y data, just prefer to create new rows and filter which rows use later.
		eg. if want to compare Y_1,...,Y_n, vs. X, don't make n columns "Y_i". Have columns "X", "Y_type", "Y_value" and filter on Y_value.
	Or going "backward" was a mistake vs. trying to recreate data and munge it together again?
	Model runs I couldn't afford to abandon while not planning adequatly was real issue



