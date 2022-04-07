
# Overclocking Commands

----

## nvidia-smi (Linux, Windows)

This tool is installed while GPU Driver installation

### Nvidia GPU list

```bash
nvidia-smi -L
```

### Core clock lock

```bash
sudo nvidia-smi --lock-gpu-clocks=<value> -i <gpu id>
```
or
```bash
sudo nvidia-smi -lgc <value> -i <gpu id>
```

when `-i`  (gpu id) not used, then for all GPUs will be applied.

#### Examples

```bash
# lock core clock for GPU with id=2 at 900 MHz
sudo nvidia-smi -lgc 900 -i 2
```

```bash
# lock core clock for all Nvidia GPUs at 900 MHz
sudo nvidia-smi -lgc 900
```

### Reset core clock lock to default/auto

```bash
sudo nvidia-smi --reset-gpu-clocks -i <gpu id>
```
or
```bash
sudo nvidia-smi -rgc -i <gpu id>
```

To reset for all GPU just drop `-i` as

```bash
sudo nvidia-smi -rgc
```

### Memory clock lock

**Only works on Nvidia 30xx**

```bash
sudo nvidia-smi --lock-memory-clocks=<value> -i <gpu id>
```
or
```bash
sudo nvidia-smi -lmc <value> -i <gpu id>
```

when `-i`  (gpu id) not used, then for all GPUs will be applied.

#### Examples

```bash
# lock memory clock for GPU with id=2 at 810 MHz
sudo nvidia-smi -lmc 810 -i 2
```

```bash
# lock memory clock for all Nvidia GPUs at 810 MHz
sudo nvidia-smi -lmc 810
```

### Reset memory clock lock to default/auto

**Only works on Nvidia 30xx**

```bash
sudo nvidia-smi --reset-memory-clocks -i <gpu id>
```
or
```bash
sudo nvidia-smi -rmc -i <gpu id>
```

To reset for all GPU just drop `-i` as

```bash
sudo nvidia-smi -rmc
```

### Power limit

```bash
sudo nvidia-smi --power-limit==<value> -i <gpu id>
```
or
```bash
sudo nvidia-smi -pl <value> -i <gpu id>
```

when `-i`  (gpu id) not used, then for all GPUs will be applied.

#### Examples

```bash
# set power limit for GPU with id=2 at 120 W
sudo nvidia-smi -pl 120 -i 2
```

```bash
# set power limit for all Nvidia GPUs at 120 W
sudo nvidia-smi -pl 120
```

----

## nvidia-settings (Linux)

This tool is installed while GPU Driver installation

### Nvidia GPU list

Note that gpus id might be different that `nvidia-smi` shows, for `nvidia-settings` commands you must use ids that `nvidia-settings` provided.

```bash
nvidia-settings -q gpus
```

### Set core clock offset

Before using should enable overclocking for GPU by `GPUTuner` or `nvidia-xconfig` tool.

```bash
sudo nvidia-settings -a [gpu:<gpu id>]/GPUGraphicsClockOffsetAllPerformanceLevels=<value>
```

To reset to default just set value to zero.

#### Examples

```bash
# set core clock offset for GPU with id=0 at 50 MHz
sudo nvidia-settings -a [gpu:0]/GPUGraphicsClockOffsetAllPerformanceLevels=50
```

```bash
# read core clock offset value for GPU with id=0
sudo nvidia-settings -q [gpu:0]/GPUGraphicsClockOffsetAllPerformanceLevels
```

### Set memory offset (memory transfer rate offset)

Before using should enable overclocking for GPU by `GPUTuner` or `nvidia-xconfig` tool.

```bash
sudo nvidia-settings -a [gpu:<gpu id>]/GPUMemoryTransferRateOffsetAllPerformanceLevels=<value>
```

To reset to default just set value to zero.

#### Examples

```bash
# set memory clock offset for GPU with id=0 at 1800 MHz
sudo nvidia-settings -a [gpu:0]/GPUMemoryTransferRateOffsetAllPerformanceLevels=1800
```

```bash
# read memory clock offset value for GPU with id=0
sudo nvidia-settings -q [gpu:0]/GPUMemoryTransferRateOffsetAllPerformanceLevels
```
