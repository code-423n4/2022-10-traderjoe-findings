### [L-01] Owner can be frontrun by users during pair creations

It's not clear what `LBPairInformation.createdByOwner` is used for, but once creating pools is made permissionless, anyone could create all important pools, setting all `createdByOwner` to `false` without possibility of changing this.