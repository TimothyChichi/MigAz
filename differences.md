# MigAz 與 Azure LAMP Example 差異

首先 MigAz 轉出之 JSON 對於 Microsoft.Compute/virtualMachines source 中的 profile 可能沒有完整描述，轉出之 JSON 檔中的內容只有
```json
"storageProfile": {
  "osDisk": {
    "name": "i-0d56f984675a7a7fa_dev_xvda",
    "osType": "Linux",
    "vhd": {
      "uri": "http://l3j13v65wcfmojixogkhvsv2.blob.core.windows.net/vhds/i-0d56f984675a7a7fa-osdisk0.vhd"
    },
    "caching": "ReadOnly",
    "createOption": "Attach"
  },
  "dataDisks": []
},
```
對於 OS 的資訊，它只提供了 osType 為 Linux ，官方文件指出，部屬時 osType 不是必需資訊。而比對 Azure 所提供之 LAMP template ，會發現 Azure 提供的資訊多了 image reference 等資訊，因此我將此段 template 改成
```json
"osProfile": {
  "computerName": "[variables('vmName')]",
  "adminUsername": "tim",
  "adminPassword": "*******"
},
"storageProfile": {
    "imageReference": {
    "publisher": "[variables('imagePublisher')]",
    "offer": "[variables('imageOffer')]",
    "sku": "[variables('ubuntuOSVersion')]",
    "version": "latest"
    },
    "osDisk": {
      "name": "i-0d56f984675a7a7fa_dev_xvda",
      "vhd": {
        "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/vhds/',variables('OSDiskName'),'.vhd')]"
      },
      "caching": "ReadWrite",
      "createOption": "FromImage"
    },
    "dataDisks": []
},
```

我在前面增加了數個 variable 方便表示，另外就是嘗試指定 OS 的詳細規格，目前這個方法式可行的，加上 image reference 後，Azure 要求我加上 osProfile ，來指定 admin user/password 。