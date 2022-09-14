# Frontend Side

The whole point of server side interface is to allow the client application use the services implemented on the server. KVision makes the process of sending and receiving data fully transparent and invisible in the frontend code. Just get the instance of your Service class using `getService()` function and use it. The only thing you need to consider is  the suspending nature of the remote methods - they have to be run inside a coroutine context (of course your code will not even compile if they are not).

```kotlin
object AddressModel {
    private val addressService = getService<IAddressService>()

    val addresses = mutableListOf<Address>()
    var search: String? = null
    var sort: Sort = Sort.FN

    suspend fun getAddressList() {
        val newAddresses = addressService.getAddressList(search, sort)
        addresses.syncWithList(newAddresses)
    }

    suspend fun deleteAddress(id: Int): Boolean {
        val result = addressService.deleteAddress(id)
        if (result) getAddressList()
        return result
    }
}   
```

&#x20;



