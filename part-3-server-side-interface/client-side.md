# Client side

The whole point of server side interface is to allow the client application use the services implemented on the server. KVision makes the process of sending and receiving data fully transparent and invisible in the client code. To make it possible you have to create a class, that will implement the service interface and will work as a proxy, allowing you to call remote methods exactly as if they were local. To do this, you inherit your class from both the service interface and the `KVRemoteAgent` class and implement all required interface methods with a special `call` method \(or more precisely a set of them\). Every time you just pass a callable reference to the method itself and all its parameters to the appropriate `call`.  

```kotlin
actual class AddressService : IAddressService, KVRemoteAgent<AddressService>(AddressServiceManager) {
    override suspend fun getAddressList(search: String?, sort: Sort) =
        call(IAddressService::getAddressList, search, sort)

    override suspend fun addAddress(address: Address) = call(IAddressService::addAddress, address)

    override suspend fun updateAddress(id: Int, address: Address) = call(IAddressService::updateAddress, id, address)

    override suspend fun deleteAddress(id: Int) = call(IAddressService::deleteAddress, id)
}
```

{% hint style="info" %}
Note: You initialize `KVRemoteAgent` class with the appropriate `KVServiceManager` object defined in the common code.
{% endhint %}

The whole code is just some boilerplate that could be auto-generated \(e.g. from annotations\), but unfortunately there are no such generators available on the Kotlin/JS platform at the moment.

The class defined above is ready to be used in the application code. The only thing you need to consider is  the suspending nature of the remote methods - they have to be run inside a coroutine context \(of course your code will not even compile if they are not\).

```kotlin
object AddressModel {
    private val addressService = AddressService()

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

 





