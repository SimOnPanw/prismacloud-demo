# Prisma Cloud Compute Container Runtime Demo

This demo is designed to showcase the power of Prisma Cloud Compute's container runtime security capabilities. 

## Description

We will deploy a container image that contains a script. If the script is started, it is ready to simulate an "attack", for example a zero day attack in your container environment.

Prisma Cloud can protect against these zero day attacks by automatically building a container runtime model. It understands the default behaviour of the container and is able to alert on or prevent against other behaviour.

If you prepare the demo environment, you start the container in a new namespace to make sure there is a new model.

To speed things up, instead of the automatic building of the model we do this manually for a short time. 

After this is done, you can continously keep running the attacks while enabling or disabling a specific Prisma Cloud rule or modifying the rule to show the capabilities of Prisma Cloud to prevent against these type of attacks without the need to maintain exceptions or a lot of rules.

## Prerequisites

- A Kubernetes cluster
- `kubectl` installed and configured to interact with your cluster
- Prisma Cloud Compute installed and protecting your cluster

## Steps

### Preparation

1. Create a new namespace

    ```kubectl create ns attacker-demo```

2. Start the container in your cluster. You can do this by running the following command:

    ```kubectl run pcc-demo --image=ghcr.io/simonpanw/prismacloud-demo:main --image-pull-policy=Always -n attacker-demo```

3. Next, manually relearn the container runtime model in Prisma Cloud Compute. This step is necessary to create a baseline for normal behavior within the container. In a production environment, this step is fully automated.

:warning: **Warning** :warning:
Do not forget the steps below, these is important for preparation of the demo.

> Go to Monitor - Runtime - Container models and find the container image. Start the manual learning.

> After a few seconds, stop the manual learning. 

4. Now, make sure that you have a runtime rule that alerts on any processes that deviate from the learned model. The scope should include our container. This rule will be used during the demo. A good starter could be to have **All other processes effect** to Alert so the attacks are not prevented when we start.

### Demo

1. Attach to the container and run the demo script:

    ```shell
    % kubectl exec -it pcc-demo -n attacker-demo -- sh
    / #
    / #./pcc_demo.sh
    ```
    
2. Now, let's change the effect of the rule from alerting to prevention for processes and domain connections outside of the model. This means that any process or connection outside of the model will not only trigger an alert, but also be stopped in its tracks.


    Go back to the demo script and press any key again. The results will be cleared and if you again press another key, the attacks will be re-run. This time, Prisma Cloud Compute will not only alert on the potential threats, but also prevent them from being carried out.

**That's it, this is how easy it is to protect your container environments against zero day attacks with Prisma Cloud!**

## Optional

Add an integration like the Slack integration show alerts coming in in real time.