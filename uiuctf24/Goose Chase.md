## Task Description
The threat group GREGARIOUS GOOSE has hacked into SIGPwny servers and stolen one of our flags! Can you use the evidence to recover the flag?
WARNING: This challenge contains malware that may read images on your hard disk. Ensure that you do not have anything sensitive present.

We're also given a dmp file and a network capture (pcap)

## Solution
#### An Easy Unintended Solution here:
```
[manogya@cntr]-[~/Desktop/comps/UIUCTF]
 > strings Goose.dmp|grep uiuctf
uiuctf{W1LD_G0OS3_CH4S3_8234819284901_UNGUESSABLE_1337}
uiuctf{W1LD_G0OS3_CH4S3_8234819284901_UNGUESSABLE_1337}
uiuctf{W1LD_G0OS3_CH4S3_8234819284901_UNGUESSABLE_1337}
uiuctf{NO_THIS_FLAG_AIN'T_IT321}YOU\?_+GITGOOSED-~i>-u

```

so yeah that's it here.  flag is `uiuctf{W1LD_G0OS3_CH4S3_8234819284901_UNGUESSABLE_1337}`
