---
title: Reflecting on our first 'major' release - Kalindis Postmortem
tags: [gamedev, postmortem]
style: 
color: 
description: Looking back on the first major project we made at Nahar Studios from a general standpoint. 
---
## Introduction
Kalindis is a mobile game my indie game 'studio', Nahar Studios (https://nahar.dev/) released back in April of 2021. It was our first (and currently only) 'major' (relative to what we've made before) release as a studio and garnered around 50 downloads (not bad for a first project!) and an average 4.9 stars on the Play Store. The game revolved around a jellyfish trying to escape the growing darkness rising below, and the main mechanic to do so involved collecting stars to replenish your jumps, as you’d lose them every time you jumped. The goal for this project was to get used to making a larger scale project as a team, as we’d typically made game jam projects and never dipped our toes into anything that took longer than week to make.

I’m writing a post-mortem now because I feel like I never actually reflected on the project post-release, and we’re struggling to get another big project started up so it may give us some insight as to what worked well and what didn’t the first time. Hopefully this is insightful to any other hobbyist game devs, especially those who are still getting started out on bigger, non-jam projects, or interested in journeying into mobile game dev.

We worked with the Godot Engine and opted for a 3D animated model as a 2D sprite. Essentially, we just modelled and animated everything, then rendered each frame as a PNG which we imported into the engine. We did our project management on Trello, held most updates and major discussions over discord, and we all typically worked from home while juggling high school work. It was our first mobile game, our first major project, and it took about 6 months to make from ideation to release.

## Part I - What went well

Kalindis was definitely a project we take a lot of pride in, having released the game while most of us were still in high school. It almost felt like a really polished, genuinely well-made mobile game you might find on the app store charts, although it definitely wasn’t perfect. The gameplay loop was simple and satisfying, although difficulty progression was nowhere near perfect, the visual identity and theming were consistent and well-polished and the music and SFX were satisfying and fit the vibe we were going for really well. Handing the game to a friend would almost always garner the reaction “You made this?!” which, as you’d imagine, would be a huge ego boost for a teenager on their first major project. We had only made lower quality jam games before this point, so achieving this level of polish and scale on our first project was a huge achievement. We even managed to push out an update to the game which brought in a leaderboard system, although there were (and still are) some issues with that.

We had a strong idea of what we wanted the game to be from start to finish. It felt like we knew what we wanted it to feel like, look like, sound like, and play like, and all we had to do was make it. The game slowly progressed with that idea in mind and that idea was what kept us on track. We tried our best not to get sidetracked with adding new features halfway through or changing up things that were working out perfectly fine for us and decided to just stick with what we thought important for the game to launch (at least until we’d released it), which turned out working well in the long run. Of course, theres no problem with being malleable, but for us we really just wanted to get a game out there, and we felt that being too malleable with our approach would slow us down and distract us, so we just drove straight down the tunnel we dug without looking at any other paths too deeply, which turned out well for us.

We were also reached out to by Lion Studios, a huge mobile game publisher owned by Applovin, who were interested in publishing the game for us and running some marketing campaigns, although we rejected that offer before it got anywhere. I'm not sure how common these offers are for mobile games, so we may have passed up on a really good deal....

## Part II – What went…not so well

Kalindis’ gameplay loop was satisfying, but it got worn out quickly without the introduction of many varying challenges and obstacles past the 20-point mark. It was simple, sure, but the gameplay progression of just making jumps a bit harder to reach as the game progressed wasn’t enough to entice players to keep playing, and oddly enough, it resulted in a weird soft barrier around the 40-point mark where the difficulty spiked. Once that was cleared, the game got easy again though. A lot of polish and playtesting was needed in the overall gameplay loop, which we evidently didn’t end up doing enough of. Admittedly, I got too lazy when it came to playing the game long enough to reach the higher numbers, so that was definitely a point worth getting play testers to run through.

Another thing which didn’t work out too well was our leaderboard system. We set it up in such a way that you could scroll through to find where people sat on the leaderboard, as well as to see the top 10, but the scrolling was messed up and didn’t work as intended. Many patches later, we gave up on it and just left it a broken mess.

Something that we did particularly badly was marketing. We made a couple of (admittedly well made) Instagram posts and that was pretty much it. No effort put in beyond that. We could’ve spammed reddit, discord showcase channels, twitter, and so on, but we did none of it. Sure, it was an indie mobile game, but a niche exists for everything, and we definitely should’ve pushed for that to get the downloads higher than 50 (most of whom were just our friends and family). Despite all of these shortcomings, there remains one which we never even got around to making, the skin shop.

The skin shop was a simple idea we had planned from the start. We were going to release the game with a few skins which you could earn through in-game currency, which we also planned to make purchasable to try and make some money back for the publishing costs on android and iOS (I’ll get to iOS in a second). We came across a lot of delays in the art team, whether it was because of personal stuff coming up or project management overambition I’m not sure, but we just barely managed to make the internal deadline we set for ourselves to actually release the project, so we opted to make skins for the game the first priority for our next update.

Of course, the skins never came. The team worked on some, but we never ended up finishing any, whether it was because of burnout, busy schedules, or lack of interest in the project I’m not sure, although it was likely a combination of the three. Since the art was out of my hands, I set out personally working on the leaderboard system while I waited, since I was sure I could finish it quickly and get the first update out before players started to lose interest.

For now, and forever, the skin shop will remain saying “Coming soon”.

We initially planned to launch for iOS and Android at the same time, but because of the annoying requirements needed to publish on iOS, we had to release on Android first to meet the announcement date of April. iOS proved to be a big headache, since you needed to do all the publishing stuff on Xcode to get it up on the store, which you can only do on a Mac (which I didn’t own). I had to borrow someone else’s, and even after doing all of the publishing setup on Xcode and submitting it for review, I couldn’t get it up because it would crash on certain iPad models. We didn’t even want to put the game on iPad, so I had a bit of a back and forth with an App Store representative before I opted to give up, since I knew nothing about Godot Engine to iPad optimization, nor did I have an iPad to test with.

The Play Store was much more cooperative, with the build we submitted being approved in less than a week, and subsequent updates being approved almost instantly.

## Part III – What to take forward

I hold a lot of love for Kalindis. Certain quality of life features and unfulfilled promises kind of held it back in the end from being a perfect first ‘big’ game to me, but it definitely means a lot to me regardless. It’s been two years since, and I have yet to work on a project as big as Kalindis. We spent 6 straight months working on it, from coming up with game design elements in paint on discord calls in the beginning to polishing the way the trajectory worked towards the end, we always had something to do, and always worked hard to try and get the game done. I’m still so proud of what we were able to accomplish with the project, but I really wish we had done a few things differently to take the game from good to great. Simple things like not introducing currency/skins without having any skins to show for it, animating the UI a bit, spending a bit more time on getting that leaderboard to do at least one thing right, giving the freeze moon mechanic some kind of visual indication for when it’s active rather than just audio, and not giving up so early on the iOS publishing. We definitely should’ve play tested more before release, and not give ourselves a deadline by announcing it to the public…. (Although the public was mainly our friends and family, it was enough for us to feel the pressure lol) In any case, we made a great game we should be proud of, and I’m glad we got it out there, despite its flaws.

## Part IV – Lessons Learned

I’ll keep this section simple:

-Don’t overscale, and don’t be afraid to cut back features to get things out there.

-Strong and consistent visual/audio language goes a long way in giving a game a polished, stylized feel, rather than feeling like an asset flip or jam game.

-Don’t give up on features and project plans because of inconveniences, but don’t stress too much about them if they don’t end up coming to fruition in the end.

-Playtest playtest playtest. Playtesting should start early and often.

-Core gameplay loop matters more than additional features, dedicate more time to getting it right.

-Don’t underestimate marketing/leave it to the last minute. Get the name out there and build an audience before you release (admittedly harder for mobile games to be fair).

I hope this was even a little bit insightful to someone! My memory is a bit hazy since it has been a couple years since we’ve touched the project, but feel free to reach out with any questions you may have.

Here's a link to the game if you'd like to try it out yourself: [play.google.com/.../kalindis](https://play.google.com/store/apps/details?id=com.nahar.kalindis&hl=en&gl=US)
