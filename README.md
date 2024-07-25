# RakNET-Fix
Fix for raknet attack issues.

# Credits
- Full credits to [Dimmy UG](https://github.com/dimmyi) and [Benjes](https://discord.com/users/714694009703890944) | [Benjes - YT](https://www.youtube.com/channel/UCTNDUi-7mRcSr9zXfYiLsuQ) for working hard to find the solution, thanks also to this wonderful team that is **Eagle Vision Brasil**.
- I don't want credit for this, he deserves it so much.

# Dependencies
- Raknet: https://github.com/katursis/Pawn.RakNet

# Settings
- In raknet settings add this: **InterceptIncomingRawPacket = true**

# Code

## Method 2
```cs
IRawPacket:20(playerid, BitStream:bs) {
    new packetid, rpcid, NumberOfBitsOfData;
    BS_ReadValue(bs, PR_UINT8, packetid, PR_UINT8, rpcid, PR_CUINT32, NumberOfBitsOfData);
    if (PacketID == 40 || (NumberOfBitsOfData >= 0x1FFFFF || NumberOfBitsOfData <= 0x80000000 || NumberOfBitsOfData < 0)) {
        printf("Crasher Detected - RPCID: %d, NumberOfBitsOfData: %d", rpcid, NumberOfBitsOfData);
        BanEx(playerid, "Intento de crashear el server");
        return false;
    }
    return true;
}
```

OR

## Method 2
```cs
IRawPacket:20(playerid, BitStream:bs) {
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
        return false;
    }

    return true;
}
```
