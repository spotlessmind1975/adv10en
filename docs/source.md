# SOURCE CODE (EXPLAINED)

Below you will find the source code of the video game. The source code has been written extensively (without abbreviations), in order to make it easier to understand. Each line has been commented to illustrate how the code works.

## INITIALIZATION (LINES 1-3)

*The order of the lines in this section is from the shortest to the longest row. In this way, we will optimize the space available in the first three lines of code.*

<pre>1 x=0</pre>

Let's set the room (**x**) to zero. Later we will modify it automatically, or based on the actions performed by the player. See also [the current room (X)](/docs/game-state.md#the-current-room-x)

<pre>t=48</pre>

This variable will store the character '0' as a PETSCII code. *This is an optimization when poking the value on the screen, and is not directly related to the game.*

<pre>print "{clear}";</pre>

Let's clear the screen (we do it only once!).

<pre>k=1061 : n=55333</pre>

Pointer to the beginning of the character video memory (k) and color video memory (n). This is where we are going to print the score, or the 37th column on the first row. On the Commodore 64, the text video memory starts from the position 1024 and the color video memory starts from 55296, so: 1024 + 37 = 1061; 55296 + 37 = 55333. *This is an optimization when poking values on the screen, and is not directly related to the game.*

<pre>w=54272</pre> 

Pointer to the beginning of the audio registers. *This is an optimization when poking the value on the audio controller, and is not directly related to the game.*

<pre>dim d$(17)</pre>

From this point on, we declarate the texts that pertain to the various components of the game (for a total of 17 texts): descriptions of the rooms, objects, error messages, titles, and so on. All texts are stored in a one-dimensional array, the index of which is calculated. The index schema guarantees an easy accessibility by means of a basic calculation. For example, the room descriptions coincide almost with the room number. The actual texts are compressed using the ["nibble compression" technique](/docs/compression.md).

<pre>poke 19,32</pre>

Change the current I/O device number to 32. Following [this guide](https://www.c64-wiki.com/wiki/Zeropage), only the first 5 bits are used. So, by setting 32, we are going to set again the keyboard but using it as a "file". This wil disable the question mark (<code>?</code>) print out on keyboard input - we will replace it with an explicit and more classic "more-than" symbol (<code>></code>).

<pre>poke 53281,0 : poke 53280,0</pre>

Now we set the background and border color of the screen ([here for a complete map](https://www.c64-wiki.com/wiki/Page_208-211)). The best is to use the black color (0) for both. This is "classic" color of text adventures.

<pre>d$(11)="{236}{075}"</pre>
Message: <code>ok</code>.

<pre>d$(10)="{094}{085}{238}{077}{063}"</pre>
Message: <code>uhm?</code>

<pre>d$(6)="{206}{089}{254}{085}{228}{088}{029}{148}{254}{033}"</pre>
Message: <code>you exited!</code>

<pre>:d$(8)="{206}{089}{254}{085}{067}{244}{242}{202}{078}{080}"</pre>
Message: <code>you see a rope</code>

<pre>d$(14)="{203}{081}{189}{254}{071}{206}{077}{074}{247}"</pre>
Message: <code>nothing more.</code>

<pre>d$(15)="{081}{244}{201}{172}{223}{243}{104}{060}{148}{247}"</pre>
Message: <code>the door is closed.</code>

<pre>d$(4)="{236}{086}{164}{031}{069}{239}{080}{148}{052}{033}{118}"</pre>
Message: <code>over the pedestal.</code>

<pre>v$="tasehl.cdrnoi***"</pre>
Here we find the "dictionary" used for [compression](/docs/compression.md). The sequence of characters has been carefully chosen, so that escape sequences are kept to a minimum. Note that only the first thirteen (13) characters are used by the encoding; the rest are filled with arbitrary characters ("*"). *The 16-character extension is required to avoid overflow situations in the decompression routine, when escape or space nibbles are found.*

<pre>d$(5)="{189}{047}{031}{172}{225}{085}{074}{143}{037}{238}{077}{066}{164}{247}"</pre>
Message: <code>in a torture chamber.</code>

<pre>2 d$(12)="{238}{042}{042}{239}{049}{110}{048}{189}{052}{047}{233}{086}{239}{042}{254}{042}"</pre>
Message: <code>** 10lines adv **</code>

<pre>d$(7)="{206}{089}{254}{085}{040}{235}{039}{241}{206}{071}{223}{251}{081}{018}{159}{173}{132}{209}{188}"</pre>
Message: <code>you can't go in that direction</code>

<pre>m$="000235160204003020000020"</pre>
This is the linearized representation of the [game map](/docs/game-state.md#game-map-and-rooms-connections). The map consists of six rooms, which are connected to each other by four directions. Each connection from one room to another is represented by a digit (0...6), which corresponds to the destination room, if you go along that certain direction (N, S, W, E). The room 0 mean "no way". Four digits make up all the connections starting from a room. There are a total of 24 digits.

<pre>d$(17)="{242}{078}{075}{238}{089}{044}{047}{175}{236}{080}{228}{044}{191}{028}{213}{235}{071}{239}{077}{172}{244}"</pre>
Message: <code>a key, a rope, nothing more</code>

<pre>d$(9)="{236}{086}{164}{031}{069}{239}{080}{148}{052}{033}{230}{044}{239}{089}{236}{085}{063}{068}{047}{239}{075}{228}{089}"</pre>
Message: <code>over the pedestal, you see a key</code>

<pre>d$(13)="{136}{239}{066}{238}{089}{045}{139}{062}{045}{242}{126}{051}{254}{048}{238}{066}{089}{239}{077}{055}{078}{080}{041}{070}{017}{253}"</pre>
Message: <code>cc by-nc-sa 3.0 by m.spedaletti</code>

<pre>d$(1)="{078}{087}{134}{236}{077}{244}{193}{159}{190}{085}{078}{071}{188}{247}{255}{081}{164}{228}{039}{243}{242}{201}{172}{047}{241}{036}{019}{247}"</pre>
Message: <code>welcome to dungeon.   there's a door at east.</code>

<pre>3 d$(3)="{189}{047}{175}{204}{254}{077}{222}{087}{081}{047}{239}{080}{148}{052}{033}{246}{188}{079}{050}{225}{044}{047}{155}{047}{159}{029}{088}{047}{102}{047}{202}{190}{085}{121}"</pre>
Message: <code>in a room with a pedestal on east, and a ditch all around.</code>

<pre>d$(16)="{189}{047}{175}{204}{254}{077}{222}{087}{081}{047}{239}{080}{148}{052}{033}{118}{207}{078}{086}{250}{081}{244}{217}{129}{229}{044}{047}{175}{236}{080}{244}{061}{095}{178}{222}{071}{235}{071}{207}{251}{036}{019}{247}"</pre>
Message: <code>in a room with a pedestal. over the ditch, a rope is hanging on east.</code>

<pre>d$(2)="{189}{031}{069}{239}{077}{157}{105}{244}{236}{070}{047}{251}{210}{099}{116}{239}{089}{236}{085}{063}{068}{031}{206}{087}{175}{204}{062}{077}{047}{241}{203}{026}{245}{178}{249}{195}{030}{085}{117}{047}{159}{204}{250}{061}{047}{241}{036}{019}{247}"</pre>
Message: <code>in the middle of an aisle. you see two rooms at north and south. a door is at east.</code>

<pre>print "{light green}";:z=1:e$=d$(12):f=len(e$):gosub9:print</pre>

Ok, so this is the first time that we call [the decompression routine that prints a description](/docs/compression.md#a-readable-decoder) (line 9). *We will call it several times.* In **e$** we put the text to be printed, in **f** we put its length, while in **z** we put the position from which to start to decompress. Before printing, we set the color green (for titles).

So, print the first line of titles.

<pre>z=1:e$=d$(13):f=len(e$):gosub9:print:print</pre>
Second line of titles (and an empty line more):

## GAME LOOP (LINES 4-8)

From this point on, the program's lines are ordered according to a game logic.

### ROOM DESCRIPTION AND SCORE (LINES 4-5)

<pre>4 print "{light gray}";</pre>

This is where the game loop's starts. The player will return to this line (line 4) each time the description of the room is needed. The room description is print using light gray color. 

<table>
    <tr>
        <td><pre>z = -
(x=0)-
(x=1)*15-
(x>0)</pre></td>
        <td><pre>it's like:
[ IF X=0 THEN Z=1]
[ IF X=1 THEN Z=15]
[ IF X>0 THEN Z=1]</pre></td>
    </tr>
</table>

The starting position depends [on the room the player is in](/docs/game-state.md#the-current-room-x) (room number zero is a special case). All rooms have a description that starts from position 1, except room 1, which has the position starting from character 15. With this expedient, *we are able to differentiate the first description*, so as to characterize the beginning of the game.

<table>
    <tr>
        <td><pre>x = -
(x=0)+
x</pre></td>
        <td><pre>it's like:
[ IF X=0 THEN X=1]
[ IF X<>0 THEN X=X]</pre></td>
    </tr>
</table>

If we're in room zero, let's move to room one (otherwise, stay there).

<table>
    <tr>
        <td><pre>e$ = d$( -
(x=0)+
x+
(x=3)*(g=1)*13)</pre></td>
        <td><pre>it's like:
[ IF X=0 THEN E$=D$(1) ]
[ IF X=3 AND G=1 THEN E$=D$(16) ]
[ IF X<>0 THEN E$=D$(X) ]</pre></td>
    </tr>
</table>

This is the description to be printed on the screen, which depends both on the room number (**x**) and, if the player is in the room no. 3, is different [if the player has used the rope or not](/docs/game-state.md#introduction) (**g**). 

<pre>f=len(e$):gosub9</pre>

Finally, print the description of the room.

<pre>poke w+4,32</pre>

Stop the sound. The sound is produced by the routine that prints the description, and it is used to give a feedback to the player.

<table>
    <tr>
        <td><pre>z = 
(x=4)*(b=0)*12+1</pre></td>
        <td><pre>it's like:
[ IF X=4 AND B=0 THEN Z=13 ELSE Z=1 ]</pre></td>
    </tr>
</table>

Now let's print a specific sentence for [the boundary conditions](/docs/game-state.md#introduction). The starting position depends on the room where the player is and the condition that [the key has not been taken](/docs/game-state.md#introduction) if the player is on room nr. 4.

<table>
    <tr>
        <td><pre>q=
(x=5)*(a=0)*8+
(x=4)*(b=0)*9+
(x=3)*(b=0)*9 :
q = q +
(x=2)*(h=0)*15</pre></td>
        <td><pre>it's like:
[ IF X=5 AND A=0 THEN Q=8 ]
[ IF X=4 AND B=0 THEN Q=9 ]       
[ IF X=3 AND B=0 THEN Q=9 ]       
it's like:
[ IF X=2 AND H=0 THEN Q=9 ]</pre></td>
    </tr>
</table>

This complex expression is used to calculate the index of which sentence to use, based on the [boundary conditions](/docs/game-state.md#introduction) and based on the [current room](/docs/game-state.md#the-current-room-x) in which the player is located.

<table>
    <tr>
        <td><pre>e$ = d$( - 
(q>0)*q-
(q=0)*14)</pre></td>
        <td><pre>it's like:
[ IF Q>0 THEN E$=D$(Q) ]
[ IF Q=0 THEN E$=D$(14) ]</pre></td>
    </tr>
</table>

Note we will print a standard message if none of the conditions are met. 

<pre>f=len(e$):gosub9:print</pre>

Finally, print the description of boundary conditions.

<pre>5 pokek,t+a+b+g+h:poken,1:pokek+1,t-1:poken+1,1:pokek+2,t+4:poken+2,1</pre>

We print the score. We do this by going directly to the video screen, and in doing so we prevent the score from disappearing due to scrolling when we arrive at the end of the screen.

### PARSER (LINES 5-6)

<pre>input"{gray}>";p$</pre>

We wait for the player to type something (in gray).

<pre>p1$=mid$(p$+m$,1,1)
p6$=mid$(p$+m$,6,1)
p7$=mid$(p$+m$,7,1)</pre>

Pass the string to the [parser](/docs/parser.md). To try to understand what the player wrote, let's examine the 3 letters on three different positions (1, 6 and 7). Note that he/she could type LESS than six characters: for this reason, we will append the string that represent the game map, in order to have enough letters. *This is an optimization, and has nothing to do with the logic of the game.*

<table>
    <tr>
        <td><pre>v = -
(p1$="n")-
(p1$="s")*2-
(p1$="w")*3
v = v - 
(p1$="e")*4-
(p1$="t")*5-
(p1$="u")*6-
(p1$="i")*7</pre></td>
        <td><pre>it's like:
[ IF P1$="N" THEN V=1 ]
[ IF P1$="S" THEN V=2 ]
[ IF P1$="W" THEN V=3 ]
it's like:
[ IF P1$="E" THEN V=4 ]
[ IF P1$="T" THEN V=5 ]
[ IF P1$="U" THEN V=6 ]
[ IF P1$="I" THEN V=7 ]</pre></td>
    </tr>
</table>
We decode the verb.

<table>
    <tr>
        <td><pre>6 o = 
(v=5)*((p7$="o")+
(p7$="e")*2)
o = o + 
(v=6)*
 ((p6$="o")+(p6$="e")*2)</pre></td>
        <td><pre>it's like:
[ IF V=5 and P7$="O" THEN O=1 ]
[ IF V=5 and P7$="E" THEN O=2 ]
it's like:
[ IF V=6 and P6$="O" THEN O=1 ]
[ IF V=6 and P6$="E" THEN O=2 ]</pre></td>
    </tr>
</table>

Next, we decode the object (if any).

### GAME LOGIC (LINES 6-7)

<table>
    <tr>
        <td><pre>a = -
(a=1)+
((x=5)*(a=0)*(v=5)*(o=1))</pre></td>
        <td><pre>it's like:
[ IF A=1 THEN A=1 ]
[ IF X=5 AND A=0 AND V=5 AND O=1 THEN A=1 ]</pre></td>
    </tr>
</table>

If action is "take rope" (v=5 and o=1), we will update the status, if the rope has not been taken already (a=1) and it is in the right room (x=5).

<table>
    <tr>
        <td><pre>b = -
(b=1)+
((x=4)*(b=0)*(v=5)*(o=2))</pre></td>
        <td><pre>it's like:
[ IF B=1 THEN B=1 ]
[ IF X=4 AND B=0 AND V=5 AND O=2 THEN B=1 ]</pre></td>
    </tr>
</table>

If action is "take key" (v=5 and o=2), we will update the status, if the key has not been taken already (b=1) and it is in the right room (x=4).

<table>
    <tr>
        <td><pre>g = - 
(g=1)-
((a=1)*(x=3)*(g=0)*(v=6)*(o=1))</pre></td>
        <td><pre>it's like:
[ IF G=1 THEN G=1 ]
[ IF A=1 AND X=3 AND G=0 AND V=6 AND O=1 THEN G=1 ]</pre></td>
    </tr>
</table>

If action is "use rope" (v=6 and o=1), we will update the status, if the rope has not been used already (g=1) and it is in the right room (x=3).

<table>
    <tr>
        <td><pre>h = -
(h=1)-
((b=1)*(x=2)*(h=0)*(v=6)*(o=2))</pre></td>
        <td><pre>it's like:
[ IF H=1 THEN H=1 ]
[ IF B=1 AND X=2 AND H=0 AND V=6 AND O=2 THEN G=1 ]</pre></td>
    </tr>
</table>

If action is "use key" (v=6 and o=2), we will update the status, if the key has not been used already (h=1) and it is in the right room (x=3).

<table>
    <tr>
        <td><pre>r=
(v>0)*(v<5)*val(
 mid$(m$,(x-1)*4-(v>0)*v-(v=0),1)
 )</pre></td>
        <td><pre>it's like:
[ IF V>0 AND V<5 THEN 
 R=VAL(MAPPA) ]</pre></td></tr>
</table>

Now let's move on to calculating what the next room would be IF the conditions were met. First, if the verb is one of the moving ones, the room should be the one indicated by the map.

<table>
    <tr>
        <td><pre>r=r - 
x*(v>4)-
x*(v=0)</pre></td>
        <td><pre>it's like:
[ IF V>4 THEN R=X ]
[ IF V=0 THEN R=X ]</pre></td>
    </tr>
</table>

However, if the verb were not moving, the next room will be always the current one. The same happens if the program doesn't understand what the player means.

<table>
    <tr>
        <td><pre>r = 
((r=4)*(g=1))*4+
((r=6)*(h=1))*6+
((r<>4)*(r<>6))*r</pre></td>
        <td><pre>it's like:
[ IF R=4 AND G=1 THEN R=4 ]
[ IF R=6 AND H=1 THEN R=6 ]
[ IF R<>6 AND R<>4 THEN R=R ]</pre></td>
    </tr>
</table>

Whichever room the player will go to, the conditions necessary for moving must be met.

<table>
    <tr>
        <td><pre>x = -
(r>0)*r-
x*(r=0)</pre></td>
        <td><pre>it's like:
[ IF R>0 THEN X=R ]
[ IF R=0 THEN X=X ]</pre></td>
    </tr>
</table>

Ultimately, if the next room is zero (0), it means that the player must stay where he/she is; otherwise, it will move accordingly.

### RESPONSE (LINE 8)

Now we have to calculate the answer. It involves testing a number of possibilities:
- that the program did not understand what the player meant;
- that the player wanted to move, but he/she cannot;
- that the player wanted to do something, but he/she could not do it.

<table>
    <tr>
        <td><pre>j = -
(r=0)-
(v=0)-
((v>4)*(v<>7)*(o=0))
j = j -
((v=5)*(o=1)*(a=0))-
((v=5)*(o=2)*(b=0))
8 j = j -
((v=6)*(o=1)*(g=0))-
((v=6)*(o=2)*(h=0))</pre></td>
        <td><pre>it's like:
[ IF R=0 THEN J=1 ]
[ IF V=0 THEN J=1 ]
[ IF V>4 AND V<>7 AND O=0 THEN J=1 ]
it's like:
[ IF V=5 AND O=1 AND A=0 THEN J=1 ]
[ IF V=5 AND O=2 AND B=0 THEN J=1 ]
it's like:
[ IF V=6 AND O=1 AND G=0 THEN J=1 ]
[ IF V=6 AND O=2 AND G=1 THEN J=1 ]
</pre></td>
    </tr>
</table>

For each of these conditions (**j=1**), there is a specific message. There is a specific message also for the condition that everything is ok (**j=0**) and the player can move to another room (or stay in the one where he/she is).

<table>
    <tr>
        <td><pre>e$ = d$( -
(j=1)*(-(r=0)*7-
(r>0)*10)-
(j=0)*(-(v=7)*17-
(v<>7)*11))</pre></td>
        <td><pre>it's like:
[ IF J=1 AND R=0 THEN E$=D$(7) ]
[ IF J=1 AND R>0 THEN E$=D$(10) ]
[ IF J=0 AND V=7 THEN E$=D$(17) ]
[ IF J=0 AND V<>7 THEN E$=D$(11) ]
</pre></td>
    </tr>
</table>

Again, there are differentiated messages, whether there is an error (**j=1**) or not (**j=0**).

<table>
    <tr>
        <td><pre>print
chr$(-(j=1)*28-
(j=0)*30)</pre></td>
        <td><pre>it's like:
[ IF J=1 THEN PRINT CHR$(28) ]
[ IF J=0 THEN PRINT CHR$(30) ]
</pre></td>
    </tr>
</table>

The color of the writing denotes whether there was an error (red color) or not (green color). Green is PETSCII 30 while red is PETSCII 28.

<table>
    <tr>
        <td><pre>z = -
(j=1)+
(j=0)*(v<7)+
(j=0)*(v=7)*((b=1)*(a=1)+
(b=0)*(a=1)*7+
(b=0)*(a=0)*13)</pre></td>
        <td><pre>it's like:
[ IF J=1 THEN Z=1 ]
[ IF J=0 AND V<7 THEN Z=1 ]
[ IF J=0 AND V=7 AND A=1 AND B=1 THEN Z=1 ]
[ IF J=0 AND V=7 AND A=1 THEN Z=7 ]
[ IF J=0 AND V=7 AND A=0 THEN Z=13 ]
</pre></td>
    </tr>
</table>

The starting point to write also depends on the message and the [boundary conditions](/docs/game-state.md#introduction). This is the logic that handles the response to be returned when inventory is requested (**v=7**). The algorithm causes the writing to be sent on the screen depending on the "possession" of objects. The entire message is written when the player owns every object, while partial message is printed if the player lacks of some objects.

<pre>f=len(e$):gosub9:print:print</pre>

Finally, print the response (and an empty line).

<pre>on j+1 goto 4,5</pre>

If the response was an error (**j=1**) then we move to the point where we ask for user input (line 5); otherwise (**j=0**) we repeat the game loop, which will propose a new room to the player (line 4).
 
<pre>9 w$=mid$(e$,z,1):y=asc(w$):
n0=yand15:n1=(y/16)and15
v0=(n0=14):v1=(n1=14):
l0=(n0<14):l1=(n1<14)
print 
 chr$(-v0*asc(mid$(e$,(-v0*z)+1,1))-
(n0=15)*32-l0*asc(mid$(v$,n0,1)));
 chr$(-v1*asc(mid$(e$,(-v1*z)+2+l0+(n0=15),1))-
 (n1=15)*32-l1*asc(mid$(v$,n1,1)));
 z=z-v0-v1+1:ifz<=fthen9</pre>

This is the text decompression routine ([see here](/docs/compression.md#a-readable-decoder) for a more readable program).

<pre>10 pokew+5,9 : pokew+6,0
pokew+24,15 : pokew+1,25
pokew,177 : pokew+4,33
</pre>

Although this area is still part of the decompression routine, we have placed some code and logic here, because there was no room elsewhere. In particular, we make a sound when we have finished printing a string.

<pre>if x<>6 or f<10 then return</pre>

If we find ourselves printing the last sentence of the game, then the game will continue running (ending). Otherwise, let's go back to the call point, to continue with the execution.