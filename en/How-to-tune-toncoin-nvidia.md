
## Tuning GPU to mine TONCOIN step by step

#### What we get

- Ubuntu 20.04 ([How to install Ubuntu](./Install-Ubuntu.md))
- Nvidia driver and CUDA installed ([How to install Ubuntu Nvidia cuda and driver](./Install-Ubuntu-Nvidia-cuda-and-driver.md))
- Nvidia GeForce GTX 1660 Ti
- GPUTuner launched under root
- Overclocking capabilities enabled for selected GPU

<img src="../images/terminal-launch-gpu-tuner.png"/>

#### Tuning

We see on GPUTuner screen info as

<img src="../images/app-launch.png" width="80%"/>

As you can see there is no mining going on and GPU has idle state:

- current core clock is only 375 MHz
- current memory clock is 405 MHz
- core temperature is about 45 C
- power draw is 19.56 W

Now let's do first a bit tuning on `ethash` algorithm.  Select `ethash` algorithm and `lolMiner` miner and hit the `Start mining` button. After some time we see these changes on GPUTuner app screen

<img src="../images/eth-01.png" width="80%"/>

- current core clock up to 1875 MHz
- current memory clock is 5750 MHz
- core temperature is about 63 C and increases fast
- power draw is 105.70 W

It seems core clock 1875 MHz too much for this GPU. To restrict the GPU to go to that high core clock value we can set a limit for this by lowering core clock or by setting power limit to the lower value. I prefer use first way and will lock core clock for a bit lower value, for example at 1530 MHz. In `Overclocking` section find `Core clock current` subsection and select there `fixed` mode, then from the list of available clocks choose 1530 MHz and click `Apply` button. After several seconds the value of `Core clock current` should also show 1530 MHz.

<img src="../images/eth-02.png" width="80%"/>

We see that temperature is still high (61 C), I'd like to get it below than 60 C. Then once again change core clock from 1530 MHz to 1200 MHz for example, and watch what will happen next.

<img src="../images/eth-03.png" width="80%"/>

Now core temperature is under 60 C as we wanted. Click `Stop mining` button. Now we are ready for `TONCOIN` profiling. Select `TONCOIN` algorithm and `lolMiner`.

#### Tuning memory transfer rate offset

First I'd like to profile GPU's memory offsets. Then we go to `Profiling` section, select `Memory transfer rate offset range` checkbox. And set there

- Start = -2000 MHz (clock to start from)
- End = 2000 Hz (clock to end with)
- Step = 50 MHz (profiling step size)

And select checkbox `Save profiling results to file` for later use.
Why we set `End` to 2000 MHz when there is `5000 MHz` available ? It is because current GPU (1660 Ti) cannot handle offset more than 2000 MHz else it freezes the system. Every GPU has its limit for maximum offset, please find it for your GPU yourself.
Then hit the `Start profiling` button.

<img src="../images/toncoin-01.png" width="80%"/>

After profiling started separate Profiling window will be opened. After profiling ended we see the result like this

<img src="../images/toncoin-02.png" width="80%"/>

Where we observe that:

- The higher memory offset the higher power consumption (draw)
- Hashrate doesn't change on memory offset changes

What means that for `TONCOIN` algorithm memory offset does nothing, and it is preferred to set it to minimum value to reduce power consumption. Click `Stop profiling` button.

#### Tuning core clock offset

Now let's do the same thing for core clock offset to find the best its value to get maximum hashrate. Select `Core clock offset range` checkbox and set there 

- Start = -1000 MHz (clock to start from)
- End = 50 Hz (clock to end with)
- Step = 50 MHz (profiling step size)

We use 50 MHz for `End` because current GPU cannot do more than that else it freezes the system. 

<img src="../images/toncoin-03.png" width="80%"/>

Then click the `Start profiling` button. After profiling completed we see result as

<img src="../images/toncoin-04.png" width="80%"/>

By this chart we see that maximum hashrate is when core clock offset set to maximum value as `50 MHz` or to `0 MHz`, but watch for power consumption, it also decreases when core clock offset goes to zero. That is we will stay with core clock offset 0 MHz or 50 MHz.

After measurements for core clock offset and memory transfer rate offset we found out that best hashrate is `862.38 MH/s` when core offset set to `0 MHz` and memory offset set to `-2000 MHz`.

But can we do better ? What to change to get hashrate higher ? And answer is yes, there is one option remains that we didn't tune yet. It is `Core clock lock` (there is also `Memory lock clock` option, but available only for Nvidia GPU 30xx series, won't describe it here, but it works the same way as `Core clock lock` does).

#### Tuning using core clock lock option

Before we start, set values for core and memory offsets that we found earlier

- To set core offset find `Core clock offset current` section, select checkbox, write there `0` MHz and click `Apply` button
- To set memory offset find `Memory transfer rate offset current` section, select checkbox, write there `-2000` MHz and click `Apply` button
- Then you can deselect checkboxes there that do not change these fields by mistake.

<img src="../images/toncoin-05.png" width="80%"/>

After this hit the `Start mining` button. On mining window we see our earlier found hashrate as 862.38 MH/s. Let's change core clock lock value, find `Overclocking` section, find there `Core clock current` and select `fixed` mode. Then select there, for example 1500 MHz and click `Apply` button.

<img src="../images/toncoin-06.png" width="80%"/>

After this the hashrate changed on our GPU to `1102.00 MH/s`. Then for our GPU these values for different core clocks

| Core clock MHz | Hashrate MH/s |
|----------------|---------------|
| 1500           | 1102.00       |
| 1710           | 1243.35       |
| 1995           | 1363.67       |

And from these table results we can conclude, to get maximum hashrate you need to increase only core clock until you are happy with core temperature. The better colling system you have the better hashrate you get.

#### Tuning using power limit option

I don't use it, but maybe for someone this instruction will help.
As we instead of core clock lock will use power limit option, then to continue set `Core clock lock` to `auto` mode. Then just click `Start mining` button. If you find that GPU got hot (it will, because we didn't limit GPU power consumption yet) then make power limit a bit lower by

- Switch `Power limit current` mode from `default` to `fixed`
- Set there for example 100 W (if you have 1660 Ti else select what suitable for your GPU)
- Apply power limit via `Apply` button

And changing power limit find the best suitable for you its value, watch for GPU temperature + hashrate. For `GTX 1660 Ti` I got such results

| Power limit (W) | Hashrate MH/s |
|-----------------|---------------|
| 100             | 1323.57       |
| 90              | 1283.46       |
| 80              | 1243.35       |
| 75              | 1223.24       |
