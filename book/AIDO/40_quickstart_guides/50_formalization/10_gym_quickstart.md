
# Gym-Duckietown AIDO tutorial {#gym-tutorial status=draft}

This is a tutorial on how to make a submission for the **LF** 
(lane following on a closed course) and **LFV** (lane following 
with dynamic obstacles) AIDO challenge. This challenge uses
the gym-duckietown environment from https://github.com/duckietown/gym-duckietown
but packs it up in a docker container. 

To this end the `gym-duckietown` environment lives in one
container called `gym-duckietown-server`. The code for the agent that 
**you** as the participant have to modify as well as the code for 
communicating with the `-server` container live in a different
repository called `gym-duckietown-agent`. We will guide you
through the process of installing everything, forking this repo, 
modifying the code and making a submission.

**TL;DR** 
There are two containers:

- `gym-duckietown-server`, runs the actual gym environment
- `gym-duckietown-agent`, hold the agent code (i.e. your code)

We provide scripts for pulling, building and launching both containers.

## Prerequisites

Currently we support development on Mac and Linux. We are working on supporting Windows soon.

Please make sure you have the following installed on your PC:

- [Docker CE](https://docs.docker.com/install/) for running the containers
- [Docker Compose](https://docs.docker.com/compose/install/#install-compose) for starting both `server` and `agent` and make them communicate with each other in one command
- Some Python code editor for modifying the agent. We'd recommend [PyCharm](https://www.jetbrains.com/pycharm/) or [Atom](https://atom.io/)
- [Git](https://git-scm.com/downloads) for pulling the repositoy.

## Install

Point your browser to this URL: https://github.com/duckietown/gym-duckietown-agent, click the `Fork` button (top right) and then click your Github account when it asks you where you want to fork to.

<figure id='screen-gym-fork'>
<figcaption></figcaption>
<img src="screenshots/screenshot-gym-duckietown-agent-fork2.png" class='diagram'/>
</figure>

Now you should see the page of your forked repo. Now let's clone that new repo. Click the green `Clone or Download` button (right side), copy the URL, open a terminal and clone your repo

    git clone https://github.com/[YOUR GITHUB USERNAME HERE]/gym-duckietown-agent.git
    
That's it for the "installation" part. :)

## Run

Change into the directoy of you `gym-duckietown-agent`

    cd gym-duckietown-agent
    
and launch both containers with this command

    docker-compose pull && docker-compose up
    
What this will do:

- Pull the latest `gym-duckietown-server` container from dockerhub (this is as of writing `1.9GB` and can take a bit to download) and run it
- Build the agent into a container (including installing dependencies within the container) and run it

Both of these actions should only take a bit longer on the first start. On consecutive starts this should be faster.

In the process of starting both containers you should see this:

<figure id='screen-gym-start'>
<figcaption></figcaption>
<img src="screenshots/screenshot-gym-duckietown-agent-startup.png" class='diagram'/>
</figure>

Then the two containers should run for a few seconds, generating steps and simulating the results. After that's done, you should see the final verdict, the average reward over 10 episodes in the terminal:

<figure id='screen-gym-end'>
<figcaption></figcaption>
<img src="screenshots/screenshot-gym-duckietown-agent-end.png" class='diagram'/>
</figure>

This second to last line (the one with `The average reward of 10 episodes was -50.7127. Best epi....`) - that's your self-evaluation. That's how you measure your own performance. You should try to get this number as high as possible, but also keep in mind that there is always some randomness involved. So even if you run this twice without changing anything, the number can change quite a lot. Later in development you can modify how many episodes are averaged so that you get a better estimate. Currently that's 10 but that's quite low. 

## Environment

- Observation: `ndarray, 120x160x3` in range `[0,255]`, corresponding to the camera image of the Duckiebot
- Reward: `float, scalar` in range [-1000,1000], corresponding to how well your Duckiebot is staying on the right lane
- Action: `tuple|list|ndarray, 2` in range `[-1,1]`, corresponding to the velocity and the steering angle, i.e.
 - `[0,0]` - means stand still
 - `[1,0]` - means full speed ahead and try to go straight
 - `[-1,0]` - means full speed backward and try to go straight
 - `[0.5,1]` - means half speed ahead and sharp right turn
 - etc.
 
*- "Try to go straight" because the Duckiebot is not perfect and will not always go perfectly straight. This happens in simulation as well as on the real robot.
On top of this there is also a minimal turn radius. The Duckietbot cannot turn on the spot. This is automatically respected when trying to turn, i.e. if you do something like `[0,+1]` the Duckiebot will **not** spin around its own axis.-* 

## Experiment

To see what is going on, please open the file [`agent.py`](https://github.com/duckietown/gym-duckietown-agent/blob/master/agent.py). You can see that the agent code follows a standard OpenAI `gym` environment:

- initialize the environment (`env.reset()`, line `21`)
- loop until episode is over:
 - pick an action (line `39`, currently this is a random action just for demo purposes. You will have to replace this)
 - execute this action (`env.step(action)`, line `45`) and get the observation & reward
 - learn from this new observation, the reward and the chosen action (not in the code)

Now it is left to you to learn from the observations and pick a better action than random.

Once you have changed the code in the `agent.py` file (for example you could try make it go straight by replacing line `39` with `action = [1,0]`), you can evaluate your agent's performance again by running

    docker-compose pull && docker-compose up
    
## Debugging / Visual Evaluation

TODO

