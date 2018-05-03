using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerMasterMover : MonoBehaviour {

    // direction shortcuts are 1=up, 2=right, 3=down, 4=left

    private float doubleTapTimer = 0f;
    private float doubleTapTolerance = .17f; // time allowed for jumping
    private int lastDirection = 0;
    private bool doubleTappingNow = false;
    private PlayerControls playerMeshScript;
    public GameObject playerBounceNode;
    public GameObject shouldBeDeadDebugIndicator;   // Some shit to visualize the state of peril
    public GameObject gameOverIndicator;            // skull, argh!

    public GameObject playerMesh;
    private GameObject flowManager;  // need to chat

    private GameObject closest = null;          // this is used to find the closest node validation position
    private GameObject closestChest = null;     // this is used to find the closest chest position
    private bool justSteppedOntoNothing = false;
    private bool lockedInDeathGuaranteed = false;
    private float deathSplashdownTimer = 0f;
    private bool isDeadNow = false;
    private bool isGameStarted = false;
    private bool canMoveNow = false;
    private float gameStartTimer = 0f;

    private float restartGameTimer = 0f;

    private int chestsHeld = 0;             // how many chests are held?
    public GameObject[] heldChestMeshes;
    public GameObject scorecardNumber;
    public GameObject restartHintText;


    public AudioSource jumpAudio;
    public AudioClip[] jumpClips;
    public AudioSource moveAudio;
    public AudioClip[] moveClips;
    public AudioSource uhOhAudio;
    public AudioClip[] uhOhClips;
    public AudioSource chestGrabAudio;
    public AudioClip[] chestGrabClips;
    public AudioSource drownAudio;
    public AudioClip[] drownClips;

    void Start ()
    {
        //Set Cursor to not be visible
        Cursor.visible = false;

        playerMeshScript = playerMesh.GetComponent<PlayerControls>();
        gameOverIndicator.transform.localScale = new Vector3(.00001f, .00001f, .00001f);
        gameOverIndicator.SetActive(false);

        flowManager = GameObject.FindWithTag("FlowManager");

        ValidateLandingPosition();

        for (var i = 0; i < heldChestMeshes.Length; i++) { heldChestMeshes[i].GetComponent<Renderer>().enabled = false; }
    }


    void Update () 
	{
        gameStartTimer += Time.deltaTime;
        if (isGameStarted)  // game is running! 
        {
            doubleTapTimer += Time.deltaTime;
        }
        else  // it's not started yet
        {
            if (gameStartTimer > 4.3f)
            {
                canMoveNow = true;
            }
            if (gameStartTimer > 10f)
            {
                isGameStarted = true;
                flowManager.GetComponent<LayoutManager>().gameIsStarted = true;
            }
        }

        if (transform.position.y < -.15f)
        {
            // JUST DROWNED!
            if (!isDeadNow)
            {
                GameOverStuff();
            }
        }
        //print("Transform for player is " + transform.position.y);

        if (Input.GetKeyDown("space"))
        {
            print("space hit");
            Application.LoadLevel("PlayArea");
        }
        if (Input.GetKeyDown(KeyCode.Escape)) //Pause game when escape is pressed
        {
            Application.Quit();
        }



        if (Input.GetKeyDown("up") && !lockedInDeathGuaranteed && canMoveNow)
        {
            // check for jumping first
            if (lastDirection == 1 && (doubleTapTimer < doubleTapTolerance))
            {
                doubleTappingNow = true;
                playerMeshScript.shouldJumpNow = true;
                JumpSoundGo();
                transform.localPosition += Vector3.forward * .1f;
                transform.eulerAngles = new Vector3(0f, 0f, 0f);
                ValidateLandingPosition();
                BounceBounce();
            }

            // ok, NOT jumping...
            else
            {
                // Am I on solid ground? Yes?  Cool, act normal
                if (!justSteppedOntoNothing)
                {
                    transform.localPosition += Vector3.forward * .1f;
                    transform.eulerAngles = new Vector3(0f, 0f, 0f);
                    ValidateLandingPosition();
                    BounceBounce();
                    MoveSoundGo();
                }
                // I'm hovering over death.
                else { lockedInDeathGuaranteed = true; }
            }

            doubleTapTimer = 0f;    // reset timer
            lastDirection = 1;
        }

        if (Input.GetKeyDown("down") && !lockedInDeathGuaranteed && canMoveNow)
        {
            // check for jumping first
            if (lastDirection == 3 && (doubleTapTimer < doubleTapTolerance))
            {
                doubleTappingNow = true;
                playerMeshScript.shouldJumpNow = true;
                JumpSoundGo();

                transform.localPosition += Vector3.back * .1f;
                transform.eulerAngles = new Vector3(0f, 180f, 0f);
                ValidateLandingPosition();
                BounceBounce();
            }

            // ok, NOT jumping...
            else
            {
                // Am I on solid ground? Yes?  Cool, act normal
                if (!justSteppedOntoNothing)
                {
                    transform.localPosition += Vector3.back * .1f;
                    transform.eulerAngles = new Vector3(0f, 180f, 0f);
                    ValidateLandingPosition();
                    BounceBounce();
                    MoveSoundGo();
                }
                // I'm hovering over death.
                else { lockedInDeathGuaranteed = true; }
            }

            doubleTapTimer = 0f;    // reset timer
            lastDirection = 3;
        }

        if (Input.GetKeyDown("left") && !lockedInDeathGuaranteed && canMoveNow)
        {
            // check for jumping first
            if (lastDirection == 4 && (doubleTapTimer < doubleTapTolerance))
            {
                doubleTappingNow = true;
                playerMeshScript.shouldJumpNow = true;
                JumpSoundGo();

                transform.localPosition += Vector3.left * .1f;
                transform.eulerAngles = new Vector3(0f, -90, 0f);
                ValidateLandingPosition();
                BounceBounce();
            }

            // ok, NOT jumping...
            else
            {
                // Am I on solid ground? Yes?  Cool, act normal
                if (!justSteppedOntoNothing)
                {
                    transform.localPosition += Vector3.left * .1f;
                    transform.eulerAngles = new Vector3(0f, -90, 0f);
                    ValidateLandingPosition();
                    MoveSoundGo();
                    BounceBounce();
                }
                // I'm hovering over death.
                else { lockedInDeathGuaranteed = true; }
            }

            doubleTapTimer = 0f;    // reset timer
            lastDirection = 4;
        }

        if (Input.GetKeyDown("right") && !lockedInDeathGuaranteed && canMoveNow)
        {
            // check for jumping first
            if (lastDirection == 2 && (doubleTapTimer < doubleTapTolerance))
            {
                doubleTappingNow = true;
                playerMeshScript.shouldJumpNow = true;
                JumpSoundGo();

                transform.localPosition += Vector3.right * .1f;
                transform.eulerAngles = new Vector3(0f, 90, 0f);
                ValidateLandingPosition();
                BounceBounce();
            }

            // ok, NOT jumping...
            else
            {
                // Am I on solid ground? Yes?  Cool, act normal
                if (!justSteppedOntoNothing)
                {
                    transform.localPosition += Vector3.right * .1f;
                    transform.eulerAngles = new Vector3(0f, 90, 0f);
                    ValidateLandingPosition();
                    MoveSoundGo();
                    BounceBounce();
                }
                // I'm hovering over death.
                else { lockedInDeathGuaranteed = true; }
            }

            doubleTapTimer = 0f;    // reset timer
            lastDirection = 2;
        }

        //print("last direction was " + lastDirection);
        //transform.localPosition += new Vector3(-.1f, 0f ,0f);



        if (justSteppedOntoNothing)
        {
            deathSplashdownTimer += Time.deltaTime;
            shouldBeDeadDebugIndicator.SetActive(true);
        }
        else
        {
            deathSplashdownTimer = 0f;
            shouldBeDeadDebugIndicator.SetActive(false);
        }

        if (!isDeadNow)
        {
            if (deathSplashdownTimer > (doubleTapTolerance * 1.1f))
            {
                GameOverStuff();
            }
        }
        else
        {
            restartGameTimer += Time.deltaTime;
            if (restartGameTimer > 3f)
            {
                //Application.LoadLevel("PlayArea");
            }
        }

    }

    void GameOverStuff()
    {
        isDeadNow = true;
        flowManager.GetComponent<LayoutManager>().gameIsOver = true;

        restartHintText.SetActive(false);

        uhOhAudio.clip = uhOhClips[Random.Range(0, uhOhClips.Length)];
        uhOhAudio.Play();

        Invoke("DrownSoundGo", 1);
        Invoke("RestartHint", 6);

        // set the score to the right number
        scorecardNumber.GetComponent<TextMesh>().text = (chestsHeld.ToString());

        gameOverIndicator.SetActive(true);
        iTween.ScaleTo(gameOverIndicator, iTween.Hash("x", .5f, "y", .5f, "z", .5f, "time", 1f, "delay", 1f, "easetype", "easeOutBounce", "islocal", true));    // POW!

        iTween.MoveTo(gameObject, iTween.Hash("x", 0f, "y", -.5f, "z", 0f, "time", 1f, "delay", .1f, "easetype", "easeInElastic", "islocal", true));    // POW!
        iTween.RotateTo(gameObject, iTween.Hash("x", 0f, "y", 1080f, "z", 0f, "time", 1f, "delay", .1f, "easetype", "easeOutQuint", "islocal", true));    // POW!
    }
    void RestartHint()
    {
        restartHintText.SetActive(true);
    }



    void ValidateLandingPosition()
    {

        GameObject[] gos;
        gos = GameObject.FindGameObjectsWithTag("NodeValidator");
        float distance = Mathf.Infinity;
        Vector3 position = transform.position;
        foreach (GameObject go in gos)
        {
            go.transform.localScale = new Vector3(1F, 1f, 1f);
            Vector3 diff = go.transform.position - position;
            float curDistance = diff.sqrMagnitude;
            if (curDistance < distance)
            {
                closest = go;
                distance = curDistance;
                justSteppedOntoNothing = false;
            }
        }
        // NOW DO THE SAME FOR BLANK SPACE DEATH NODES!!!
        gos = GameObject.FindGameObjectsWithTag("NodeValidatorBlankSpace");
        foreach (GameObject go in gos)
        {
            go.transform.localScale = new Vector3(1F, 1f, 1f);
            Vector3 diff = go.transform.position - position;
            float curDistance = diff.sqrMagnitude;
            if (curDistance < distance)
            {
                closest = go;
                distance = curDistance;
                justSteppedOntoNothing = true;
            }
        }

        //print("found the nearest node, and it is " + distance + " away.");
        closest.transform.localScale = new Vector3(1F, 15f, 1f);

        // ok, now quantize and attach to that object
        transform.parent = closest.transform.parent;
        transform.localPosition = new Vector3(0, 0, 0);

        // snap to lock the nearest rotation
        //print(transform.localEulerAngles.y);
        if (transform.localEulerAngles.y > 315 || transform.localEulerAngles.y < 45)
        { transform.localEulerAngles = new Vector3(0, 0, 0); }
        if (transform.localEulerAngles.y > 45 && transform.localEulerAngles.y < 135)
        { transform.localEulerAngles = new Vector3(0, 90, 0); }
        if (transform.localEulerAngles.y > 135 && transform.localEulerAngles.y < 225)
        { transform.localEulerAngles = new Vector3(0, 180, 0); }
        if (transform.localEulerAngles.y > 225 && transform.localEulerAngles.y < 315)
        { transform.localEulerAngles = new Vector3(0, 270, 0); }


        // OK, now check for booty!
        GameObject[] go2s;
        go2s = GameObject.FindGameObjectsWithTag("BootyChest");
        float distance2 = Mathf.Infinity;
        Vector3 position2 = transform.position;
        foreach (GameObject go2 in go2s)
        {
            Vector3 diff2 = go2.transform.position - position2;
            float curDistance2 = diff2.sqrMagnitude;
            if (curDistance2 < .005)
            {
                Destroy(go2);
                chestsHeld++;
                flowManager.GetComponent<LayoutManager>().chestsHeld += 1;
                justSteppedOnChest();
            }
        }

    }


    void BounceBounce()
    {
        iTween.MoveTo(playerBounceNode, iTween.Hash("y", .04f, "time", .1f, "easetype", "easeOutQuart", "islocal", true));    // UP!
        iTween.MoveTo(playerBounceNode, iTween.Hash("y", 0f, "time", .1f, "easetype", "easeOutQuart", "delay", .1f, "islocal", true, "oncomplete", "ResetPosition", "onCompleteTarget", gameObject));    // LANDING
    }

    void ResetPosition()
    {
        // clear out error when done
        //print("RESET POSITION");
        playerBounceNode.transform.localPosition = new Vector3(0, 0, 0);

    }

    void justSteppedOnChest()
    {
        print("CHEST!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!! Now at " + chestsHeld);
        flowManager.GetComponent<LayoutManager>().PickedUpChest();

        chestGrabAudio.clip = chestGrabClips[Random.Range(0, chestGrabClips.Length)];
        chestGrabAudio.Play();

        // OK, now show them bootys!
        for (var i = 0; i < heldChestMeshes.Length; i++)
        {
            if (i < chestsHeld)
            {
                heldChestMeshes[i].GetComponent<Renderer>().enabled = true;
            }
            else
            {
                heldChestMeshes[i].GetComponent<Renderer>().enabled = false;
            }
        }
    }

    void JumpSoundGo()
    {
        jumpAudio.clip = jumpClips[Random.Range(0, jumpClips.Length)];
        jumpAudio.Play();
    }
    void MoveSoundGo()
    {
        moveAudio.clip = moveClips[Random.Range(0, moveClips.Length)];
        moveAudio.Play();
    }

    void DrownSoundGo()
    {
        drownAudio.clip = drownClips[Random.Range(0, drownClips.Length)];
        drownAudio.Play();
    }

}
