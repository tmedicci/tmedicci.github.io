---
layout: post
title:  ":uk: Home Theater DIY (Loudspeakers, Amplifiers and HTPC) - Part 1"
author: Tiago
date:   2024-02-18 15:00:00 -0300
categories: projects
tags: project audio sound electronics
background: '/img/posts/2024-02-18-home_theater_diy_1/projeto_sketchup.jpg'
---

Home Theater DIY - The Saga of the Loudspeakers
===============================================

*Este artigo também foi publicado em português e pode ser acessado [aqui]({% post_url 2024-02-18-home_theater_diy_1 %}).*

**This post was replicated from another post that also originated from a replica. I explain: originally (as I'll talk about soon), I posted about the loudspeakers on HTFORUM - a Brazilian forum to discuss home-theater systems, which is no longer accessible. Then, I reported the saga of the DIY Home-Theater project (which is the content of this post) on the Discourse instance of the Hacker Laboratory of Campinas (LHC), which can be accessed [here](https://discourse.lhc.net.br/t/home-theater-diy-caixas-de-som-amplificadores-e-central-de-midia/238), in Portuguese. So, here is the content replicated from LHC's Discourse and translated to English :wink:**

## The Idea of the Post

So that this endeavor does not start the way (normally) other endeavors start ~~and end~~, the idea of this post is that it will be updated with the most current information about my sound system. It starts, of course, with the construction of my first loudspeakers and the first "version" of the system. I reproduce below, then, the post I made in the extinct (and missed) HTFORUM, where I reported my saga that started around 2008. The first report was made in December 2012. The following was written by that date and, now, translated:

> ## Bookshelf Speakers with Akron Drivers
>I start writing this post by making my intentions clear: to introduce myself to the forum, to thank the users who helped me directly or indirectly in this project that, for sure, strongly influenced my professional choice and, of course, to present details of the construction, tips, and share experiences and other reports about the project in the title. Namely, two bookshelves would be built, bass reflex with Akron speakers, built by the magnificent Paulo Ramos, plus 4 amplifiers of 60W each, a circuit called PL1050 that, in my view, presents a great cost x benefit ratio, combined with the simplicity of the electronic circuit.
>
> First, about how all this started: I never knew exactly where my taste for music and sound in general came from. It was not something inherited from my parents or someone very close. All I know is that, since I was a young child, I used to sit in front of the radio listening to music and, thus, came that feeling of well-being. Then, when I was 15/16 years old, partly due to a lack of money to buy a home theater and partly due to the desire to know a little more about electronics, I looked for some audio amplifier projects on the internet. An older cousin, who had some experience with homemade electronics, agreed to help me with the assemblies that would come. It was also at this time that I met HTFORUM. I was always amazed to see the projects that the users made and posted, as well as the final result of them.
>
> I would also need a pair of loudspeakers for my amplifiers. I will first talk about the speakers: after downloading tutorials and articles on the subject, I realized that it was not an easy task to build good speakers, both due to the complexity of the variables and the lack of good components accessible in the Brazilian market. In my wanderings through the forum, I noticed that Mr. Paulo Ramos, also a visitor of the forum, was selling a kit with his speakers and there was a good opportunity for quality speakers for the speaker project. A little apprehensive, I contacted Paulo Ramos, owner of Akron, whose products were (and are) a reference of quality in the market. The contact was returned and, as soon as possible, and I arranged to pick up the speakers at Paulo Ramos' residence, since we lived in neighboring cities. I was 16 years old at the time. Here go my first thanks to this person who helped me a lot in the speaker project: Paulo Ramos received me and my father very well, invited us for a brief audition of his Spalla Bookshelves in his living room and, by e-mail, there were numerous doubts that he answered me always with patience and sympathy. I owe a large part of my interest in acoustics and loudspeakers to him, who assisted me with the smallest details of building the pair of bookshelves.
>
> The project of the Bookshelves:
> {% cloudinary /img/posts/2024-02-18-home_theater_diy_1/projeto_sketchup.jpg alt="Bookshelves' project done using SketchUp" %}{: .img-fluid}
>
>The speakers are a bass reflex type with a vent behind the box. On the baffle, there is a 6-degree difference to the vertical plane to compensate for the temporal phase difference between the tweeter and the midbass speakers. Internally, both on the baffle and the back, a certain quality plywood was used (without hollow spaces or imperfections), the rest of the material is MDF, apart from the duct, made with PVC pipe and molded at the ends to improve air dynamics. The speaker place-holder was made using a 6mm MDF piece on top of the baffle, where the speaker goes through the 6mm piece and fits into the lower piece.
> {% cloudinary /img/posts/2024-02-18-home_theater_diy_1/DSC01929.jpg alt="Baffle" %}{: .img-fluid}
>
>
> The photo above is of the baffle, in detail. Something to be commented on in this part is that, if you do not have a wood router available, make sure to leave plenty of clearance space to be able to sand later and leave the correct circumference. I had to fix it with plastic mass since the hole was poorly made by a carpenter.
>
>
>
> {% cloudinary /img/posts/2024-02-18-home_theater_diy_1/PB220026.jpg alt="Woodworking Problems" %}{: .img-fluid}
>
>
> Like every beginner in woodworking, I had several problems in building the wooden structure, especially when it came to finishing. Fortunately, the plastic mass helped me correct errors and homogenize the box surface. A tip worth mentioning here is not to use, on the same piece, plastic mass and white mass. The white mass is a little better to be used to correct small errors, while the plastic mass serves for more grotesque errors.
>
>
>
> {% cloudinary /img/posts/2024-02-18-home_theater_diy_1/DSC01928.jpg alt="Back of the Box" %}{: .img-fluid}
>
>
> The image above shows in detail the back of the box, the vent outlet (made with PVC pipe and molded in a glass bottle, at the ends), as well as the location of the box terminals. It is worth mentioning here that I used an acrylic piece as a terminal fitting, a cheap material that served the function well.
> Also from acrylic, I made the support for the frequency divider used:
>
> {% cloudinary /img/posts/2024-02-18-home_theater_diy_1/PB220002.jpg alt="Filter Construction" %}{: .img-fluid}
>
>
> The filter is a LinkWitz-Riley type, 12 dB/8th, with a pole at 2.5 kHz. The capacitors are 4uf, metallized polypropylene (MKP) from EPCOS, purchased at Casa dos Capacitores, in the Sta. Efigênia region in São Paulo and the inductors ordered at Líder transformers, also in the region. The resistors used are ceramic and carbon. In addition to the filter, a Zobel network was also used, in parallel with the midbass for correction of its impedance curve and, finally, an L-pad circuit to attenuate the tweeter, since the tweeter has a higher sensitivity than the midbass. The entire circuit is mounted on the board above which was fixed to the back of the box, near the terminals. For the internal wiring, the 4mm Power Cable from Tiaflex was used. The box was internally lined on the sides with bituminous felt (that lining used under carpets) and also a little synthetic cotton behind the midbass. The tips for the construction of the frequency divider and internal lining are also credited to Mr. Paulo Ramos. Again, thank you very much.
>
> I found the contact terminals at Dual Comp, also in the Sta. Efigênia region. They are metal terminals with a good finish. The final result:
>
> {% cloudinary /img/posts/2024-02-18-home_theater_diy_1/DSC_0064.jpg alt="Detail of the Terminals" %}{: .img-fluid}
>
>
>
> The coating was done with black PET laminate. The material is relatively easy to work with and offers a good finish. (I could open a topic just to talk about the laminate, but if anyone shows interest, I can comment more about it). An important tip is: for the finish, after applying the laminate, pass a stylus (with a new blade) very close to the wood to remove the burr on the sides, and then use a black wax chalk on the sides to remove any imperfections. It looks very good. I recommend the material. It is easy to clean. Also, using silicone (those for car tires) gives a very good finish. In the photos above the finish was not clean.
>
>
> More details of the box construction [here](https://photos.app.goo.gl/Z9TeD2hX6prqqupN8).
>
>
> The speaker box project started with the first email sent to Paulo Ramos, who helped me immensely throughout the conception of the project. However, due to my lack of planning, lack of appropriate materials and tools, and lack of time, mainly, it was shelved for some (a lot of) time. It was a year of entrance exams, I had to decide the direction of my professional career, dedicate myself to something that I was sure was my vocation: Electrical Engineering and, speaking of electrical, we then move on to the details of the amplifiers:
>
> ## PL1050 Amplifiers
> When you are 15 years old and don't know anything about electronics, everything is new and surprising. You start by looking for known and simple projects to be assembled and tested. It was at this moment that I came across the PL1050 amplifier project, which was sold as a kit by a store in São Paulo. The project consists of an amplifier that delivers 50W, at 8 Ohms, with an asymmetric power supply of 36V. By swapping the pair of output transistors, TIP120/125 for TIP122/127, the circuit diodes for BA315 diodes, and increasing the power supply voltage to 40V, you get higher output power and better circuit response. The circuit topology is this:
>
> {% cloudinary /img/posts/2024-02-18-home_theater_diy_1/circuito_eletrico.jpg alt="PL1050 Amplifier Circuit" %}{: .img-fluid}
>
>
> It seemed very simple to assemble. Anyone who wants more details, just ask and I will send the PDF with the layout of the boards and other details. In any case, just search the internet for PL1050, it will not be difficult to find this material. I made the PCB using the thermal method, that is, you print, on a laser printer, the inverted layout on a transparent paper and then transfer this layout to the board with the iron. Very simple.
>
>
> {% cloudinary /img/posts/2024-02-18-home_theater_diy_1/DSC01905.jpg alt="PL1050 Amplifier Boards" %}{: .img-fluid}
>
>
>
> The image above shows 5 boards of the amplifier circuit, still without the power transistors and diodes, which would be mounted on the heatsink. Here is an important point to be taken into account: the amplifier circuit cannot be classified as high-end. The speakers are a notch above the amplifiers. However, the circuit is extremely simple in its conception, very cheap and with excellent cost x benefit. I can say for sure that it was a great choice to start messing with electronic assemblies. The first version assembled, from the photo above, was tested on a reasonable quality system, with Sony speakers, from the late 70s (I don't remember the model, unfortunately, but it was not integrated), very well preserved and of great quality (3 ways, 12" woofer) and the amplifier, powering such speakers pleased a lot in the result, both to me and to the owner of them. I recommend this circuit, it will not disappoint beginners and did not disappoint me as one of the first electronic assemblies I made when I was just 16 years old.
>
>
> And, to improve the performance of the amplifier, with the help of my cousin, we adapted a radio power supply to give the 40V we needed. The chosen circuit had very low noise, short-circuit protection and great stability. It keeps the output voltage practically constant after the thermal stabilization of the components. (to those interested, I can also talk more about the regulated power supply circuit). We also integrated an overvoltage protection circuit, triggered by a relay that "turns off" (disarms) the power supply.
>
> {% cloudinary /img/posts/2024-02-18-home_theater_diy_1/DSC01922.jpg alt="PL1050 Amplifier: power supply board" %}{: .img-fluid}
>
> As you can see, capacitors were used that together added up to almost 20000uF, all to guarantee power to the bass.
>
> The definitive board, made of fiberglass, also had the tracks tinned, as shown in the image above. Like the power supply board, the definitive boards of the amplifiers were also made using a fiberglass board. At this point, it is worth highlighting the poor quality of the phenolic board. Despite being easier to cut, the phenolic board is not very resistant to the heat of the soldering iron and its copper tracks come off easily, something that rarely happens with fiberglass.
>
> Finally, I ended up making 4 amplifiers, two of which are being used to amplify the bookshelves. The cabinet that houses the amplifiers was made of wood (my mistake, it would have been better if it was metal, but it ended up being a bit large). For signal control, I used Alpha's linear potentiometers in parallel with 1% resistors, a technique used to simulate a logarithmic effect that presents a good result, as described in: http://sound.westhost.com/project01.htm. The signal interconnection wiring of the amplifiers was made with shielded cables from Tiaflex that have excellent grounding mesh. It is also important to ground the metal casing of the potentiometer to eliminate hums and other disturbances in the volume control. The sensitivity of the amplifier is low, being able to amplify signals from a CD player or a computer.
>
> Photo of the (almost) ready amplifier cabinet:
>
> {% cloudinary /img/posts/2024-02-18-home_theater_diy_1/DSC_0048.jpg alt="PL1050 Amplifier: cabinet" %}{: .img-fluid}
>
>
> I will still change the potentiometer knobs. Both potentiometers are double, the one on the left controls the stereo channel, and the one on the right, not yet finished, will control two amplifiers with mono signal, for a future subwoofer project. The quality of the photo was not very good, but I used white acrylic on the front of the cabinet, with holes where I put LEDs behind the acrylic plate, obtaining this effect. In the photos, the LED glow seems very strong, but it is not.
>
>
> More photos of the set of amplifiers + regulated power supply [here](https://photos.app.goo.gl/E7MyamDQseFUjDjr5).
>
>
> Forum friends, this is my first big post. Excuse me if I detailed it too much in some sections. Please let me know about possible errors and correct me. Comments, addenda, doubts and questions will always be very welcome. If needed, I can detail more about some processes, whether in the manufacture of the boxes or the amplifiers, as well as provide more details about the amplifiers and the power supply. I would like to record that this project, despite its simplicity, strongly influenced my professional choice and brought great learnings, technical and life. I started thinking about it when I was 15, I finished the project of the boxes and the amplifiers at the beginning of the year, close to turning 21. Many years have passed from the idealization to the end of the project: lack of time, lack of planning, lack of tools, lack of parts and solutions to the problems encountered. All this served for one purpose: learning. I am already thinking about the new projects that will come after this one, now with a little more maturity and planning. Shortly after I started the project, came the moment of professional choice, entrance exam, relocation, etc... I chose electrical engineering as a profession, Campinas as my new city, and Unicamp, as my college. I am going to my last year of college with the certainty that the choice was well made and still a dream (a little remote, due to market possibilities) to specialize and work in the area of audio, electroacoustics and electronics aimed at this segment.
>
> Once again, thank you to the forum visitors for the help. I always closely followed various posts and achievements of friends. Thank you, especially, to Mr. Paulo Ramos for all the help during the conception of the Bookshelf project. Thanks also to my cousin Carlos, for the help in the assemblies and patience in teaching some electronics tips.
>
>
>
> Cordial hugs and see you soon, I am at your disposal!

This was the post that I rescued (*rest in peace*) from the HTFORUM. Forgive the verbose speech and the many thanks. In particular, I can say that the bookshelf speakers are wonderful and still serve me today!

The PL1050 amplifiers are surprising for their cost-benefit. They are great considering the ease of assembly and cost. The power supply is oversized for this amplifier, as well as the transformers.

Wait for the new chapters of this story.

See you!
