# growblock
Script that checks / resizes a DigitalOcean block storage based on current usage.

Requires [jq](https://stedolan.github.io/jq/) and [doctl](https://www.digitalocean.com/community/tutorials/how-to-use-doctl-the-official-digitalocean-command-line-client) installed.

## Usage:
~~~
growblock [action] [opts]...
Check if a DigitalOcean block storage needs resizing and optionally resize it
Actions are: --check | --resize | --help
--help : this message
--check : return 0 if volume needs to be resized and 1 otherwise
  -m MOUNT | --mount=MOUNT : (required) mountpoint of storage to check
  -t THRESHOLD | --threshold=THRESHOLD : threshold before triggering resize
     Threshold values:
     80% (default) = less than 20% free
     20 or 20G = less than 10G free
--resize : perform the resize if threshold is reached (accepts all --check options)
  -d | --dry-run : do not perform any actions
  -i ID | --id=ID : ID of DigitalOcean storage to resize. Either name or id required
  -n NAME | --name=NAME: name of DigitalOcean storage to resize. Either name or id required
  -g GROW | --grow=GROW: amount
     Grow values:
     20% (default) = add 20% to current size
     20 or 20G = add 20G to current size
Other opts: 
 -c CONFIG | --config CONFIG : doctl config file
 --context CONTEXT: doctl context
 -v | --verbose : verbose output
~~~

## Examples:
### Check if volume needs to be resized
Checks if volume mounted on `/mnt/my_block` needs resizing and a program based on that check
~~~
growblock --check --mount /mnt/my_block && echo Resize needed
~~~

This uses the default threshold of 80% full.

### Check with specified threshold
Specifies that volume needs to be resized at 90% usage
~~~
growblock --check --threshold 90% --mount /mnt/my_block
~~~

Specifies that volume needs to be resized if under 10G free
~~~
growblock --check --threshold 10G --mount /mnt/my_block
~~~

### Resize dry-run
Print actions that would have been taken on resize
~~~
growblock --resize --dry-run --mount /mnt/my_block --name my-block
~~~

### Resize
Perform the resize online
~~~
growblock --resize --mount /mnt/my_block --name my-block
~~~

### Resize with specified threshold / amount
Specifies that volume should grow by 20G when free space falls below 10G
~~~
growblock --resize --mount /mnt/my_block --name my-block --threshold 10G --grow 20G
~~~

Specifies that volume should grow by 20% when free space falls below 10%
~~~
growblock --resize --mount /mnt/my_block --name my-block --threshold 90% --grow 20%
~~~
