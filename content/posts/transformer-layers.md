---
date: '2024-08-31T22:29:03+05:30'
draft: false
title: "Extending *Transformer layers as Painters* to DiT's"
summary: "Exploring the possibility of a common represention space among the layers of a DiT"
---

The motivation for this experiment comes from "Transformer layers as Painters"<sup><a href="#ref1">[1]</a></sup>. by [Sakana AI](https://huggingface.co/SakanaAI) and [Emergence AI](https://huggingface.co/EmergenceAI) who suggest the existence of a common representation space among the layers of an LLM because of the residual connections.

I try to replicate the same for diffusion transformer models like Flux, SD3 and AuraFlow.

The main questions from the paper are:
1. Do layers use the same representation space?
2. Are all the layers necessary?
3. Are middle layers all doing the same function?
4. Does the layer order matter?
5. Can we run the layers in parallel?
6. Does order matter for some tasks more than others?
7. Does looping help parallelized layers?
8. Which variants harm performance the least?

#### Terminology used here:
Transformer layers or MM-DiT layers as referred to here have two streams for dealing with text embeddings and image embeddings seperately while also having a joint attention mechanism.

Single layers or Joint layers deal with encoder embeddings and image embeddings together a.k.a single flow blocks in Flux arch listed above.

Grouping of the layers based on cosine similarity is done into first layers, middle layers and last layers as in the paper.

The following layer execution strategies are used for the experiment:
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/l81R2mdOUI_psN7mOW-AX.png)
*Fig 1: Layer execution strategies*

### TL;DR

1. Flux shows the most prominent grouping (based on activation cosine similarity) of layers indicating the possibility of a common representation space followed by AuraFlow. But all 3 models do show grouping indicating a common representation space.
2. The layers before and after a group of layers seem to act as 'translation' layers, converting the model representation from one space to another. This is evidenced by the fact that removing preceding layers is catastrophic.
3. Skipping some layers from a group of layers degrades image quality the least compared to other methods. This is in line with the finding of the paper.
4. Repeating the same layer from a group is the worst (apart from removing the so called 'translation' layers which don't belong to the group anyway)
5. Repeatedly executing the layers in parallel and averaging their outputs is not catastrophic for layers that give prompt adherence but catastrophic for layers that deal with aesthetic quality. Same with reversing the middle layers.

Expand to see the prompts used:

<details>
  
1. A charismatic speaker is captured mid-speech. He has short, tousled brown hair that’s slightly messy on top. He has a round circle face, clean shaven, adorned with rounded rectangular-framed glasses with dark rims, is animated as he gestures with his left hand. He is holding a black microphone in his right hand, speaking passionately. The man is wearing a light grey sweater over a white t-shirt. He’s also wearing a simple black lanyard hanging around his neck. The lanyard badge has the text “Anakin AI”. Behind him, there is a blurred background with a white banner containing logos and text (including Anakin AI), a professional conference setting.
2. a red dog wearing a blue hat sits with a yellow cat wearing pink sunglasses
3. A Samsung LED moniter's screen on a table displays an image of a garden with signboard mentions 'All is Well', A teddy toy placed on the table, a cat is sleeping near the teddy toy, a mushroom dish on red plate placed on the table, raining outside, a parrot sitting on the nearby window, a flex banner with text 'Enjoy the life' visible from outside of the window,3d model of a green war balloon, clash of clans, fantasy game, front view, game asset, detailed, war ready, photorealistic, in a war enviroment, spring, disney style, pixar style
4. Photo of a felt puppet diorama scene of a tranquil nature scene of a secluded forest clearing with a large friendly, rounded robot is rendered in a risograph style. An owl sits on the robots shoulders and a fox at its feet. Soft washes of color, 5 color, and a light-filled palette create a sense of peace and serenity, inviting contemplation and the appreciation of natural beauty.
5. The Golden gate bridge.
</details>

### Results on Flux-Schnell
Flux architecure has two different transformer blocks. The one referred to here as transformer block/layer is a MMDiT block which has two streams, one for encoder hidden states and one for hidden states. The single transformer block/layer is single stream which acts on hidden states. See the architecture<sup><a href="#ref3">[3]</a></sup>

![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/AlRT6lZDwt_T-U42rElwa.png)
*Fig 2: Cosine Similarities for activations averaged over timesteps & inputs*

#### Skip
* Skipping the first MM-DiT block is not catastrophic but shows the role it plays in converting (translation) the prompt to the representation space. And the last layer converts (translation) it to the space of the single layers.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/-3ys9nvv1YYH9s1CrAdxs.png)

* Skipping MM-DiT layers from the middle group affects the finer details of the image while retaining the broad concepts of the prompt. (pink glasses are present but on dog. Robot is no longer made of felt etc...)

* Skipping single layers affects the visual quality. There are two distinct middle layer groupings in the Flux single layers. The first seems to be responsible for building the structural layout and broad details whereas the following group deals with finer details.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/inT59pxDSjmUqfZfEaAvM.png)

* Skipping single layers preceding the middle group affects the aesthetics and results in visual hallucinations: (multiple instances of same subject. eg: multiple parrots) and missing details (bridge) which can indicate incorrect 'translation' of the prompt to details.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/WUubROzUdffXqSqSb_Xfj.png)

#### Middle Repeat
* Repeating the same layer multiple times is catastrophic. Paper theorizes that this is because it pushes the data out of distribution from what the model has trained to handle.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/KCydWN15G7rivnL5mMXhV.png)

#### Reverse
* Reversing MM-DiT layers retains some concepts from the prompt but details are completely lost. Reversing single layers is catastrophic. 
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/jguS_PaCtgXiKKfyPeJv9.png)

#### Parallel
* Executing the middle layers in parallel and averaging their outputs is catastrophic.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/FdYuR_DoUDv7aphMkyGe3.png)

#### Looped Parallel
* Interestingly for the MM-DiT layers, running the middle layers in parallel and averaging the outputs and passing the output to the layers in parallel again and repeating this loop recovers image generation capabilites to a large extent but for single layers it is catastrophic.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/Qxn_7K1jFz5miF8lHmDzB.png)

### Results on SD3
The cosine similarities for SD3 activations are not as strong as seen for Flux middle layers. Also there are only MM-DiT blocks for SD3.

Looking at both the activations, it seems like the middle group can be split into lower middle (indices below 14) and higher middle (indices above 14)
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/cokA5WyIYAPu6XvfuupnT.png)

#### Skip
* Same as for flux, the layers lower in order seem to add structural layout and the ones higher in order add the fine grained details. Skipping first layer is catastrophic, adding weight to the translation layer hypothesis.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/wx6d6NwE6cX-kNhyg3Jfx.png)

#### Middle Repeat
* Repeating lower middle layers is catastrophic but outlines of the concepts can still be made out. Higher layers are less worse.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/hhoW9d3_iCR9qjRA8NPy0.png)

#### Reverse
* Reversing the middle layers keeps the concepts but destroys a lot of visual quality.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/XcvcoNt4AKRe_i395fNTF.png)

#### Parallel
* Executing lower middle layers in parallel is catastrophic but for higher layers it keeps the layout and destroys visual quality.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/w4tFPVhDMfzaiZUjwOLxE.png)

#### Looped Parallel
* This is only slightly better than just executing in parallel.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/S9ukC_2S_21BGOBQ7Tjb0.png)

### Results on AuraFlow v0.2
Auraflow which preceded Flux has a similar architecture but the number of MM-DiT blocks are much less (4 vs 19). 
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/bkuV4Mk-Qc-dsEtrc9UkD.png)

#### Skip
* Skipping first MM-DiT block is catastrophic. Middle one degrades quality but keeps adherence. Last layer is a bit more damaging. Interestingly skipping first and last single layers is less damaging than for middle layers.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/yh4AQLGUJvKvYbBKUF_38.png)
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/7A-_f1hRoRqGWqrdxLUCk.png)

#### Middle Repeat
* Repeating middle MM-DIT layer is catastrohic as always but repeating a few single layers affects visual quality without being catastrophic. This is not comparable to the other test results as only 3 layers very repeated instead of all middle layers
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/IeE4z3gjT3oy02XLRhaPG.png)

#### Reverse
* Reversing MM-DiT layers causes some visual artifacts while keeping the layout but single layer reversing causes unnatrual layout and subject mdofications. Extending this, reversing all middle single layers would be catastrophic like before. Here only a few middle single layers have been reversed
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/GjPveJEetSIiIifNM22tj.png)

#### Parallel
* Executing in parallel affects visual quality.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/2izsY2-iHnnLMm40L5Fcm.png)

#### Looped Parallel
* Executing in looped parallel for MM-DiT layers causes artifacts but for single layers, as long as not all layers are used, it only slightly degrades the quality from baseline in most cases.
![image/png](https://cdn-uploads.huggingface.co/production/uploads/63158cbb8be84e1ab4162510/jyeojsVK22JEsJk-SYG6O.png)

### Follow up
Based on this distinction of layers and the roles they seemingly play, a natural question is how would applying LoRA to specific layers affect the training and image generation during inference ?

Edit: See https://x.com/__TheBen/status/1829554120270987740 It actually makes a difference! You don't need to train LoRA's on all layers.

### References & Citations
<a id="ref1">1. [Transformer layers as painters](https://arxiv.org/abs/2407.09298) <br>
<a id="ref2">2. [diffusers/pipelines](https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines) <br>
<a id="ref3">3. [Flux architecture diagram by @nrehiew_](https://x.com/nrehiew_/status/1820404554795802684) <br>