Threading, Async, and timers
============================
When using timers or infinite statements such as ``while(true)`` or ``wait`` it is important to use ``thread`` before the function in order to prevent crashes or freezes.

What is ``thread``?
-------------------
The ``thread`` term is used to tell squirrel to run the following function **separately** from the rest of the game, while in a simple scripts code is run sequentially(line 1,2,3 etc)
if a line of code would last forever or need to function in parallel to normal gameplay, such as a  ``wait`` command, it is important to use ``thread`` or the game will get stuck processing that line indefinitely
and will not move to the next lines, causing crashes or freezes. 

How do i use ``thread``?
------------------------
Using thread is fairly simple, if we have a function called ``delayannouncement`` that chooses one player as "it" 10 seconds after spawning we cannot use this function on its own, instead calling it with a thread by simply calling

``thread delayannouncement()``

The same applies to a ``while(true)`` function, for example ``almostover`` a function that checks every 5 seconds to see if the game has 2 or less minutes left and announces it if so.

``thread almostover()``

Example Script
--------------
lets try implement both of our scripts from the previous 2 sections, as well as a callback to trigger the script.

First, lets add our callback to the gamemodes core function. 

.. code-block:: javascript^^

    global function GamemodeTag_Init


    void function GamemodeTag_Init(){
    AddCallback_GameStateEnter( eGameState.Playing, MatchStart )
    }

Then lets define the function matchstart and have it simply thread our two important functions.

.. code-block:: javascript

    void Matchstart{
        thread delayannouncement()
        thread almostover()
    }

This script waits 10 seconds, picks a player and announces that player as "it" however being ``it`` currently does nothing, we will define that later.

.. code-block:: javascript

    void delayannouncement(){
    wait 10.0 
    string chosenplayer = GetPlayerArray()[RandomInt(GetPlayerArray().len())]
    string message = chosenplayer + " is it!"
    foreach ( entity player in GetPlayerArray() )
        SendHudMessage( player, message, -1, 0.4, 255, 0, 0, 0, 0, 3, 0.15 )
    }

This function will now repeat endlessly, waiting 5 seconds before each repeat. make sure to add a ``return`` or ``break`` statement to prevent the message looping every 5 seconds after, unless you want that

.. code-block:: javascript

    void almostover(){
        while(true){
            if(GameTime_TimeLeftSeconds() < 120){
                foreach ( entity player in GetPlayerArray() )
                    SendHudMessage( player, "Two minutes left!", -1, 0.4, 255, 0, 0, 0, 0, 3, 0.15 )
                    break
                }
                wait 5.0
            }
        }

You can also set up some code to be executed when a thread ends:

.. code-block:: javascript

    void killPlayerAfterAfewMoments(entity player) {
        OnThreadEnd(
            // you have to explicitely capture all variables you want to use inside function
            function() : ( player )
            {
                if ( !IsValid( player ))
                    return

                SendHudMessage( player, "Time to sleep, fella!", -1, 0.4, 255, 0, 0, 0, 0, 3, 0.15 )
                player.Die()
            }
        )

        // Do something time consuming
    }

    thread killPlayerAfterAFewMoments( GetPlayerArray()[0] )


You have now created and threaded both functions.

Signals and flags
----------------------

Signals
^^^^^^^^^^

Signals and flags allow threads to wait for events before running some code.

For example, if we want to tell a player not to give up after being killed several times, we can write it this way:

.. code-block:: javascript

    // First, we register signal we want to use
    RegisterSignal("OnMultipleDeaths")


    void function WatchForDeaths (entity player) 
    {
        int deathsCount = 0

        while( GamePlayingOrSuddenDeath() )
        {
            if ( player.isDead() )  // This doesn't exist, don't try this at home
            {
                deathsCount += 1

                if (deathsCount >= 42)
                {
                    // This sends "OnMultipleDeaths" signal on player entity
                    player.Signal( "OnMultipleDeaths" )
                } 
            }
        }
    }


    void function DontGiveUp (entity player)
    {
        // This is a blocking call
        player.WaitSignal("OnMultipleDeaths");

        // This will not run until entity received "OnMultipleDeaths" signal
        SendHudMessage( player, "Don't give up!", -1, 0.4, 255, 0, 0, 0, 0, 3, 0.15 )
    }

    // Launch our methods in dedicated threads
    entity player = GetPlayerArray()[0]
    thread WatchForDeaths( player )
    thread DontGiveUp( player )

In this example, the ``DontGiveUp`` method is launched at the same time as ``WatchForDeaths``; but it will not 
run until player died 42 times.

When you want your thread to die on a given event, you can use ``entity.EndSignal( "OnMultipleDeaths" )``; when said signal 
is set, thread will end (after calling any `OnThreadEnd` methods).

Flags
^^^^^^^^^^

``Flags`` work pretty much the same way as ``Signals``, except they can be set up without target entity:

.. code-block:: javascript

    // create flag
    FlagInit( "BombHasExploded" )

    // wait for it
    FlagWait( "BombHasExploded" )

    // update it
    FlagSet( "BombHasExploded" )
    FlagClear( "BombHasExploded" )
    FlagToggle( "BombHasExploded" )

    // get its current value (returns a boolean)
    Flag( "BombHasExploded" )