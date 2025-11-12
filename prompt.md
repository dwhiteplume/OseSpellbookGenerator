This effort is to allow a user to request a spellbook which characters may find. The functionality is based on *Old Schools Essesntials: Advanced Player's Tome* including **Advanced Speel Book Rules (Optional Rule)** from p112.

I would like the user interface to be as phone friendly html page with embedded CSS and JavaScript. User choices should be in a form and output in a textarea.

Include the following:
* a simple dark/light mode switcher which uses local storage for remembering the user's preference
* a copy results button
* a "Random" button which sets all user coices to their respective random selection and randomly chooses to use fudge dice

### Page Metadata

We need to add meradata to the page for social media previews. Include the following:

```html
<meta name="fediverse:creator" content="FEDIVERSE">
<meta property="og:title" content="OSE Spellbook Generator">
<meta property="og:description" content="Create a random spell book for OSE with either magic-user or illusionist spells.">
<meta property="og:image" content="AVATAR">
<meta property="og:url" content="PAGEURL">
<meta property="og:type" content="website">
```

### UIX

1. We should allow the user to declare the spell book owner's class (magic-user or illusionist) or choose randomly for them.
2. We should give the user the option to either declare the spell book owner's Intelligence score (between 3 and 18), choose the average (10), or have the score randomly determined (between 3 and 18).
3. We should allow the user to specify the level of the caster (between 1 and 14) or let it be chosen randomly.
4. Allow an option for *Fudge Dice*. If the user selects the fudge option roll 4dF, meaning roll four dice with sides -1,0,1 and total them. If the amount is positive, add that number of new spells to the book, randomly spread across the available levels (for example, the spellbook of a level 5 illusionist could have spells of levels 1, 2, and 3; if our fudge roll is +2 then randomly select two spells from any available level, such as one more level 1 and one more level 3). If the result is negative, mark two random spells from the final list as "incomplete", meaning the reader can determine the intent, but they are not copied successfully and may be completed with additional research. If the result is zero we make no change.  

Once we have this information we must determine the base number of spells for the book.

1. Get the number of spells from level 1 based on the intelligence decided earlier. These are the total number of spells at level 1, so if the spell book belonged to a level 1 magic-user the resultant number of spells would be the base number of spells in the book. If the spell book owner's level is above 1 then this base number replaces the level 1 entry and we add to it for progressive levels (more below).

| Intelligence | Beginning Spells |
| :----------: | :--------------: |
| 3-5          | 1                |
| 6-9          | 2                |
| 10-14        | 3                |
| 15-17        | 4                |
| 18           | 5                |

2. Get the total number of spells based on level and add them to the base as follows:

| Level | 1 | 2 | 3 | 4 | 5 | 6 |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| 1   | +0 | 0 | 0 | 0 | 0 | 0 |
| 2   | +1 | 0 | 0 | 0 | 0 | 0 |
| 3   | +1 | 1 | 0 | 0 | 0 | 0 |
| 4   | +1 | 2 | 0 | 0 | 0 | 0 |
| 5   | +1 | 2 | 1 | 0 | 0 | 0 |
| 6   | +1 | 2 | 2 | 0 | 0 | 0 |
| 7   | +2 | 2 | 2 | 1 | 0 | 0 |
| 8   | +2 | 3 | 2 | 2 | 0 | 0 |
| 9   | +2 | 3 | 3 | 2 | 1 | 0 |
| 10  | +2 | 3 | 3 | 3 | 2 | 0 |
| 11  | +3 | 3 | 3 | 3 | 2 | 1 |
| 12  | +3 | 4 | 3 | 3 | 3 | 2 |
| 13  | +3 | 4 | 4 | 3 | 3 | 3 |
| 14  | +3 | 4 | 4 | 4 | 3 | 3 |

Special: at caster level 1 we determine spell count based on intelligence. As the spell book owner rises in caster level they obtain more spell level 1 spells. In this chart caster level 1 has +0..+3 for spell level 1 spells, indicating that the number is the base number, decided by intelligence roll, and then +N based on level. So, an illusionist's spell book, with an 18 intelligence, at caster level 5 would have 5 base spell level 1 spells, +1 level one spell for progression, as well as two level 2 spells and one level 3 spell.

3. At this point we should have an object indicating how many spells of each spell level are present in the spell book. For our illusionist example it would be as follows:
```pseudocode
$NumberOfSpells = @{
	1:  6,
	2:  2,
	3:  1,
	4:  0,
	5:  0,
	6:  0,
}
```
4. If fudge dice were indicated we roll 4dF and get the result:
    a. **4dF > 0** randomly pick an available spell level for each positive integer and add 1 to it. Never add to a zero. Thus if `4df = 3. we might randomly add another spell level 1, another spell level 3, and another spell level 1, giving us this based on prior example:
```pseudocode
$NumberOfSpells = @{
	1:  8,
	2:  2,
	3:  2,
	4:  0,
	5:  0,
	6:  0,
}
```
    b. **4dF == 0** we make no changes to the object.
		c. **4dF < 0** we remember this number and will apply it later.

5. Determine spells by looking at the number of known spells per level and randomly selecting (unique) from the list for each one, for each level. The complete list is below.
6. If our 4dF roll was negative we get to make it fun. For each negative integer we need to take action
    a. If the spellbook has any 0 entries (indicating no spells of that level are known) we should randomly select one spell of _any_ unknown level, add it to the spellbook and mark it as "incomplete".
		b. For any ramaining integers, or if there are no 0 count spell levels, randomly pick one existing spell from the spell book and mark it as "incomplete". This should be unique. Never mark a spell "incomplete" twice.
7. Provide the user the spell book list ordered by level (numerical) then spell name (alphabetical).

## Spell List

#### Illusionist Spell List

##### 1st Level
1. Auditory Illusion
2. Chromatic Orb
3. Colour Spray
4. Dancing Lights
5. Detect Illusion
6. Glamour
7. Hypnotism
8. Light
9. Darkness
10. Phantasmal Force
11. Read Magic
12. Spook
13. Wall of Fog

##### 2nd Level
1. Blindness / Deafness
2. Blur
3. Detect Magic
4. False Aura
5. Fascinate
6. Hypnotic Pattern
7. Improved Phantasmal Force
8. Invisibility
9. Magic Mouth
10. Mirror Image
11. Quasimorph
12. Whispering Wind

##### 3rd Level
1. Blacklight
2. Dispel Illusion
3. Fear
4. Hallucinatory Terrain
5. Invisibility 10' Radius
6. Nondetection
7. Paralysation
8. Phantom Steed
9. Rope Trick
10. Spectral Force
11. Suggestion
12. Wraithform

##### 4th Level
1. Confusion
2. Dispel Magic
3. Emotion
4. Illusory Stamina
5. Improved Invisibility
6. Massmorph
7. Minor Creation
8. Phantasmal Killer
9. Rainbow Pattern
10. Shadow Monsters
11. Solid Fog
12. Veil of Abandonment

##### 5th Level
1. Chaos
2. Demi-Shadow Monsters
3. Illusion
4. Looking Glass
5. Major Creation
6. Maze of Mirrors
7. Projected Image
8. Seeming
9. Shadowcast
10. Shadowy Transformation
11. Time Flow
12. Visitation

##### 6th Level
1. Acid Fog
2. Dream Quest
3. Impersonation
4. Manifest Dream
5. Mass Suggestion
6. Mislead
7. Permanent Illusion
8. Shades
9. Through the Looking Glass
10. Triggered Illusion
11. True Seeing
12. Vision

#### Magic-user Spell List

##### 1st Level
1. Charm Person
2. Detect Magic
3. Floating Disc
4. Hold Portal
5. Light
6. Darkness
7. Magic Missile
8. Protection from Evil
9. Read Languages
10. Read Magic
11. Shield
12. Sleep
13. Ventriloquism

##### 2nd Level
1. Continual Light
2. Continual Darkness
3. Detect Evil
4. Detect Invisible
5. ESP
6. Invisibility
7. Knock
8. Levitate
9. Locate Object
10. Mirror Image
11. Phantasmal Force
12. Web
13. Wizard Lock

##### 3rd Level
1. Clairvoyance
2. Dispel Magic
3. Fire Ball
4. Fly
5. Haste
6. Hold Person
7. Infravision
8. Invisibility 10’ Radius
9. Lightning Bolt
10. Protection from Evil 10’ Radius
11. Protection from Normal Missiles
12. Water Breathing

##### 4th Level
1. Charm Monster
2. Confusion
3. Dimension Door
4. Growth of Plants
5. Hallucinatory Terrain
6. Massmorph
7. Polymorph Others
8. Polymorph Self
9. Remove Curse
10. Curse
11. Wall of Fire
12. Wall of Ice
11. Wizard Eye

##### 5th Level
1. Animate Dead
2. Cloudkill
3. Conjure Elemental
4. Contact Higher Plane
5. Feeblemind
6. Hold Monster
7. Magic Jar
8. Pass-Wall
9. Telekinesis
10. Teleport
11. Transmute Rock to Mud
12. Transmute Mud to Rock
13. Wall of Stone

##### 6th Level
1. Anti-Magic Shell
2. Control Weather
3. Death Spell
4. Disintegrate
5. Geas
6. Remove Geas
7. Invisible Stalker
8. Lower Water
9. Move Earth
10. Part Water
11. Projected Image
12. Reincarnation
13. Stone to Flesh
14. Flesh to Stone
