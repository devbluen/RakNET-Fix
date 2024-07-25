# RakNET-Fix
Fix for raknet attack issues.

# Credits
- Full credits to [Dimmy UG](https://github.com/dimmyi) for working hard to find the solution, thanks also to this wonderful team that is **Eagle Vision Brasil**.
- I don't want credit for this, he deserves it so much.

# Dependencies
- Raknet: https://github.com/katursis/Pawn.RakNet

# Settings
- In raknet settings add this: **InterceptIncomingRawPacket = true**

# Code
```cs

stock GetDifferenceValue(value1, value2)
{
    new difference = value1 - value2;
    if (difference < 0) difference = -difference;
    return difference;
}

IRawPacket:20(playerid, BitStream:bs)
{
    new rpcid, numberOfBitsOfData;
    BS_ReadValue(bs, 
        PR_IGNORE_BITS, 8,
        PR_UINT8, rpcid,
        PR_UINT32, numberOfBitsOfData
    );

    new bits;
    BS_GetNumberOfBitsAllocated(bs, bits);

    new diff = GetDifferenceValue(bits, numberOfBitsOfData);
    if (numberOfBitsOfData > 8192 || diff > 512)
    {
        new blockIp[32];
        GetPlayerIp(playerid, blockIp, sizeof blockIp);

        printf("Blocked package: playerid: %d | rpcid: %d | bits used: %d | allocated bits: %d | diff: %d", playerid, rpcid, numberOfBitsOfData, bits, diff);
        BlockIpAddress(blockIp, 60000);
        return 0;
    }

    return 1;
}
```
