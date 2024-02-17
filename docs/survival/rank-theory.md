---
comments: true
---

# Rank Theory

Original Post: [栈位理论](https://wiki.pvz1.com/doku.php?id=%E6%8A%80%E6%9C%AF:%E6%A0%88%E4%BD%8D%E7%90%86%E8%AE%BA) [Rank Theory]

Author: Ataunerry, echochrome, i8l97i, OTZzzz, sqrt_7, 我是沙雕www

Translator: Mirai

## Preface

==There isn't a real 'stack' in PvZ, what PvZ has is a structure that behaves like a stack.== For inner logic, check *DataArray*[^3]. For convenience, in this article, we will use expressions like 'stack', or 'stack rank' (or rank for short).

For tools showing stack rank and demonstration video, check *PvZ Rank Theory*[^4].

## Basics

**This section is only for explanation, and is not the real behavior of RAM.**

A stack is like a hole, if we put some items in it in sequence, the last item that came in will be the first to come out, this is called a Last In, First Out (LIFO) structure. The game only has access to the top of the whole stack. 

In the case of plants, all plants on the lawn have a serial number, representing the position of the plant in an array. An initialization of a game (e.g. restarting a level) will cause a stack to be initialized to allocate serial numbers to plants. The initialized stack only contains one element 0, and whenever a plant is planted, the following events will take place:

1. The top element of the stack popped out (came out) and became the serial number of the new plant;
2. If the stack is empty, push the next integer of the element just popped into the stack.

Whenever a plant disappears (e.g. shoveled, after a while of being smashed[^n1], explosion of an ash plant, exploded by a jack, etc.), its serial number will be pushed into the stack and become the top of the stack, ready to be allocated to next planting.

For example, if there are four plants A, B, C, and D on the lawn, having a serial number of 0, 1, 2, and 3, respectively, then now stack will be like [4], only containing an element 4. After shoveling C, the serial numbers of A, B, and D will remain to be 0, 1, and 3, and the stack will be like [2, 4], having an element 2 as its top. After planting a new plant E, The four plants on the lawn A, B, D, and E will have a serial number of 0, 1, 3, and 2, while the stack [4] only contains element 4.

With the same mechanic, the serial number of zombies is maintained by another stack, and when a zombie dies, its serial number is pushed back into the stack.

The expression 'low stack rank' is equivalent to 'small serial number'.

## Sequence Number

==The concept only has usage in some behaviors of bungee zombies and dancing zombies, so it isn't necessary to be familiar with it.==

The game distinguishes different plants and zombies with their stack rank and sequence number, though most behaviors only depend on stack rank. The sequence number is sometimes called 'mRank counter', but in RAM it's called mNextKey. The game maintains three counters with an initial value of 0 for plants, zombies, and bullets. When a plant, zombie, or bullet spawns, the game will assign the current counter value to its sequence number, and add 1 to the counter[^n2].

The counters will be reset to 0 when the level is re-entered, which might create flaws in bungee zombies' plant-grabbing and dancing zombies' backup-zombie-controlling.

There is controversy over considering operations that require sequence-number-controlling as a part of the Rank Theory. 

## Behaviors relevant to the rank of plants

### Dolphin rider zombie

Depending on the relative rank between plants, dolphin rider zombies can jump over a tall-nut.

<div align=center><img src="../pict/rank-theory/dolphin-jumping-over-a-tall-nut.png" alt="Dolphin riders jumping over a tall nut."/></div>

#### Static (No operation during the whole process)

Notice that the behavior only appears on dolphin riders that jump just after entering the pool (i.e. there's a plant in the 8th column). So it's a rumor that the tall-nut of Classical 10 Cob will be jumped over if protected by a pumpkin. 

The conditions of this behavior are:

1. A tall-nut is on the 8th column, it doesn't matter whether it has a pumpkin or not;
2. a. An empty pumpkin is on the 7th column, (b. or a tall-nut is on the 7th column, it doesn't matter whether it has a pumpkin or not);
3. a. The empty pumpkin (b. or the tall-nut) on the 7th column has a lower rank than the tall-nut on the 8th column.

If conditions 1, 2a, and 3a are met, then the dolphin rider will jump over the tall-nut on the 8th column and reach the left part of the 7th column; if conditions 1, 2b, and 3b are met, then the dolphin rider will jump over the 8th column but be blocked by the tall-nut on the 7th column and reach the right part of the 7th column.

One might think the dolphin riders' unexpected jumping over is probability-based. If the conditions above are met, the probability of jumping over is 100%, otherwise, it's 0%. There isn't any probability between them, and the inconsistency of the behaviors is just because the experimenter doesn't control the relative rank of these plants well.

#### Dynamic (Operate during the jumping process)

If we plant during the jumping process, there will be more possibilities.

For a tall-nut in the n-th column (1≤n≤7), if a non-tall-nut plant with a lower rank than the tall-nut is planted in the n+1-th column with precise timing, then the dolphin rider will jump over the tall-nut. The latest timing is 123cs after jumping (the determination of blocking happens in 124cs), and the earliest timing is roughly when the dolphin rider has half of his head in the border of the n+1-th column and the n+2-th column[^n3].

Similarly, we can let the dolphin rider try to jump over the tall-nut (or pumpkin) in the n-th column, but be blocked by a tall-nut in the n+1-th column. Planting a lily pad and a tall-nut with a lower rank in the n+1-th column after the dolphin rider starts to jump and before it's blocked in the 124th centisecond will simply do the job. Notice that the operation cannot be done with other plants in the n-th column.

### Determination order to plants

If the zombie can attack (smashing for gargantuars, biting for others) plants in two cells, the plant with a lower rank will be attacked first. If a flower pot or a pumpkin is in these slots, the sequence will be slightly more complicated.

In the cobbed setups, using sacrificing plants with a lower rank than the cob might lower the probability of gargantuar smashing the cob.

In the setup with back cobs[^t1], if the pumpkin (having a ladder or not doesn't matter) has a lower rank than the cob, then imps with an x-coordinate of 230px won't bite the cob; otherwise, they will bite the cob.

### Bungee zombies

Bungee zombies rely on the stack rank and the sequence number to determine which plant to grab. If we can let our target plant have the same stack rank and sequence number as the plant the bungee zombie chose, then the target plant will be grabbed instead. Many unusual behaviors can be done exploiting this: grabbing a cob, a cob with a back wheel in the air, and normal plants floating on the roof or the pool without a flower pot or a lily pad[^n4], to suffice the developing aesthetics.

<div align=center><img src="../pict/rank-theory/bungee-grabbing-a-cob.png" alt="A bungee zombie grabbing a cob." style="width:300px;" /></div>

Reproduction processes:

1. After the bungee zombie's target is summoned and before the bungee zombie starts to close up his hands, re-enter the level and place an arbitrary plant in the targeted cell, then re-enter the level again before he starts to close up his hands.
2. After the bungee zombie starts to close up his hands and before he finishes this action (i.e. touches the plant), shovel the plant just placed and place the plant that is intended to be grabbed.
3. Now the target plant still exists on the lawn, and a picture of this plant is in the cell the bungee zombie has targeted. You should let the flower pot or the lily pad be the target plant if you want to make a floating plant and place the plant that is intended to float on the target plant as usual.
4. After the bungee zombie finishes closing up his hands (i.e. has touched the plant), the target plant will disappear and the picture of it will levitate with the bungee zombie. In this stage, you can't place plants on the grabbed target plant.

???+ note "Explanation"
	- In the first stage, the bungee zombie hasn't determined the stack rank and the sequence number of the grabbed plant. The first re-entering sets the sequence number of the first plant planted as 0. The second re-entering prepares a counter with a value of 0 for the target plant.
	- In the second stage, the bungee zombie determined the stack rank and the sequence number (0) of the first plant. We shovel the first plant to push its stack rank back to the stack, so the target plant we placed afterward will have the same stack rank and the same sequence number (0) as the first plant, confusing the bungee zombie.
	- Before the bungee zombie finishes his hand-closing process, we can still interact with the target plant. If the target plant is a lily pad or a flower pot, we should place the plant that is intended to float in this stage.
	- Note: If the position of the bungee zombie can be controlled, the first re-entering and placing can be done earlier, because its purpose is only to create a plant with a sequence number of 0.

An example setup with a back-wheel-floating cob is Jellyfish 14 Cob[^6].

<div align=center><img src="../pict/rank-theory/jellyfish-14-cob.png" alt="Jelly 14 Cob setup." style="width: 500px"/></div>

### Failure of ICE3

Using plants that interchange effecting timeout (e.g. coffee bean, imitater), the problem in switching calculation will cause a 1cs change to the total effecting delay. For ICE3[^12], the relative rank between the coffee bean and the ice-shroom will affect the total effecting delay, making ICE3 easy to fail.

In scripted play, the solutions include modifying the timing function and reading the RAM. Some consider these as rule-breaking.

## Behaviors relevant to the rank of zombies

### Tangle kelp & chomper

These two plants will attack zombies with a lower rank in priority.

### Dropped zombie

We already know that the determination of zombies' biting is biting down 4HP every 4cs after it spawns. The dropping of a bungee zombie takes 375~394cs. If the zombie is dropped onto a plant, then:

1. If the dropping time is a multiple of 4cs, then the relative rank of zombies affects the first bite - if the dropped zombie has a lower rank, then the first bite happens 4cs after it drops, otherwise, the first bite happens as soon as it drops.
2. If the dropping time isn't a multiple of 4cs, then the first bite happens when the next biting determination occurs, regardless of the relative rank.

### Sacrifice plant

When multiple zombies are determining the smashing, vaulting, hammer-raising, etc., the determination starts from the one with the lowest rank.

Example:

1. When placing a plant in the 9th column in Night Endless, sometimes we can find the plant is immediately smashed. The smashed plant causes some of the gargantuars to raise their hammer, while others keep walking. This is because the plant is in the attack area of these gargantuars and a catapult zombie. 1cs after it is placed, all the gargantuars with a lower rank than the catapult zombie raise their hammer to the plant, and then the catapult zombie smashes the plant, finally, the gargantuars with a higher rank ignore the smashed plant.


2. When placing a flower pot on the roof, sometimes we can find the immediately smashed plant causes some of the pole vaulting zombies to vault, while others keep sprinting. The vaulting zombies have a lower rank than the zomboni.

<div align=center><img src="../pict/rank-theory/sacrifice-plant-to-gargantuars.png" alt="Sacrifice a grave buster to some gargantuars." style="height: 250px; padding-right: 100px"/><img src="../pict/rank-theory/sacrifice-plant-to-pole-vaulting-zombies.png" alt="Sacrifice a flower pot to some pole vaulting zombies." style="height: 250px"/></div>

The stack rank of zombies cannot be controlled in normal games, so these sacrifices under vehicles are not rigorous and are unacceptable. [^n5]

### Pole vaulting zombie

In vaulting over plants, pole vaulting zombies have similar behaviors to dolphin rider zombies, see [above](#dolphin-rider-zombie).

There is a bug in the interaction between pole vaulting zombies and potato mines. A potato mine enumerates all the zombies with the same row[^n6] and checks their rank. Every pole vaulting zombie without a pole will shift the triggering area to the right for 40px (i.e. half of a column), and an armed potato mine is invincible[^n7]. That means at least two vaulted zombies can produce an invincible potato mine. When the number of vaulted zombies is so big that the triggering area is in the right of the spawning position of every zombie, then the potato mine will be fully immune to the zombies' biting of its row. Mob Switch 22 Cob[^7] is an application.

In the first image below, three vaulted zombies with a maximum serial number of 48 are on the left, while a buckethead zombie with a serial number of 93 and a football zombie with a serial number of 131 is biting an invincible mine. 

<div align=center><img src="../pict/rank-theory/invincible-mine.png" alt="Three vaulted zombie have make a mine invincible." style="height: 200px; padding-right: 100px"/><img src="../pict/rank-theory/4vaulting-1mine.gif" alt="Four vaulted zombie trigger a mine without getting hurt." style="height: 200px"/></div>

Multiple vaulted zombies can shift the triggering area right enough to protect zombies from getting hurt by the explosion. In 'I, Zombie', it's common to use f【FE】双冰24炮——冰菇的精准our vaulted zombies to trigger potato mines.

A single vaulted zombie can also trigger a mine in a pumpkin, which has some applications in some modified versions of PvZ.[^13]

For a demonstration of the bug check[^8], for an analysis check[^9].

### Interception bug

In this section, we consider the time an instant damage triggers gargantuars to throw imps is 0cs. We already know that the effecting timing of ICE3 is 105cs (210cs if gargantuars are slowed). However, the earliest intercepting time is 107cs (212cs if slowed). This is because, in 106cs (211cs if slowed), there is a bug that might fail the interception. There is even a case that the gargantuar is killed but the imp is still alive, see the images below.

<div align=center><img src="../pict/rank-theory/doom-shroom-triggering-bug.png" alt="A doom-shroom triggers the interception bug." style="height: 200px; padding-right: 100px"/><img src="../pict/rank-theory/interception-bug.png" alt="The gargantuar is killed but the imp is still alive." style="height: 200px"/></div>

- If the imp has a higher stack than the gargantuar, then at the moment the imp has been summoned for 1cs, the interception will success;
- Otherwise, the imp has just been summoned and its coordinate is uninitialized (and still be the remaining data in RAM), and will be teleported into the gargantuar's hand after 1cs. The interception will probably fail.

### Dancing zombie

A dancing zombie uses stack rank and sequence number to identify his backups. If we can somehow let another zombie have the same stack rank and same sequence number with a backup, then it will be misidentified as a backup.

In Survival Endless, New Prom Night[^10] replaces a hypnotized backup to a normal zombie, producing perpetual glooms without Maid Strategy. 

In I, Zombie, applications include level *Grafting*[^11].

[^1]: Elovi (2021). [【愚人节礼物】海豚？撑杆？高坚果？你想不到的秘密竟然是](https://tieba.baidu.com/p/7283448252) [[April Fool's Day Gift] Dolphin Rider? Pole Vaulting Zombie? Tall-nut? The secret you can't imagine is ...]
[^2]: 向量cwl (2020). [关于大家都知道的栈位理论](https://tieba.baidu.com/p/6549539823) [On the well-known Rank Theory]
[^3]: Ghastasaucey (2024). [DataArray](https://wiki.pvz1.com/doku.php?id=%E6%94%BB%E7%95%A5:dataarray)
[^4]: 向量cwl (2020). [PvZ 栈位理论](https://www.bilibili.com/video/BV1oE41157SY) [PvZ Rank Theory]
[^5]: 猪一样的猪 (2021). [【RE】二十炮 全悬空开局](https://www.bilibili.com/video/BV16v411w7Mh) [[RE] 20 Cob full floating opening]
[^6]: 星绵㭍奈 (2021). [[PE]水母十四炮_极速开局](https://www.bilibili.com/video/BV1Rq4y1N72a/) [[PE] Jellyfish 14 Cob blitz opening]
[^7]: Elovi (2021). [【FE】伪和平双九列二十二](https://www.bilibili.com/video/BV1nq4y1k7i6) [[FE] Mob Switch Double 9-col 22 Cob]
[^8]: Elovi (2021). [无敌土豆雷](https://www.bilibili.com/video/BV1Mq4y197xZ) [Invincible Potato Mine]
[^9]: Ghastasaucey (2021). [关于PVZ1的撑杆土豆偏移bug的浅析](https://www.bilibili.com/read/cv12417001/) [Some simple analysis on the vaulted-mine bug in PvZ1]
[^10]: 李华的27号速记员 (2023). [[翻修] NE. 新·舞夜惊魂 常规开局](https://www.bilibili.com/video/BV1uk4y1i73a) [[Improved] NE. New Prom Night normal opening]
[^11]: sqrt_7 (2023). [【IZE珍珑】移花接木（新版翻修）](https://www.bilibili.com/video/BV1ev4y1k769) [[IZE Level] Grafting (improved)]
[^12]: 身入苦海销尘垢 (2012). [【FE】双冰24炮——冰菇的精准](https://tieba.baidu.com/p/1405342382) [[FE] Double-ice 24 Cob - Precision of ice-shroom]
[^13]: Ataunerry (2023). [改版通略](https://wiki.pvz1.com/doku.php?id=%E6%94%BB%E7%95%A5:%E6%94%B9%E7%89%88%E9%80%9A%E7%95%A5) [A general introduction to modified versions]
[^n1]: The smashed plant that is still visible also has a serial number.
[^n2]: The mechanic has been simplified, for detail check *DataArray*[^3].
[^n3]: If the planting operation is earlier than the bound, the dolphin rider will be blocked and bite the tall-nut as usual.
[^n4]: The floating plants can also be done using catapult zombies[^5].
[^n5]: Spikeweeds and squashes can be used to make all the gargantuars raise their hammers because they are immune to smashing from the vehicles, and spikeweeds destroy vehicles 26cs after they are placed.
[^n6]: The mechanic has been simplified. Zombies in different rows will be ignored during the enumeration of every zombie.
[^n7]: Immune to biting, but can still be damaged by smashing from vehicles and gargantuars, explosions from jack-in-the-box zombies, bullets from zombies in ZomBotany, flames from jalapeno zombies, and basketballs from catapult zombies. 
[^t1]: Translator: the cob on the 4th and the 5th column.
