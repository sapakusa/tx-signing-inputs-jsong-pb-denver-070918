
# Signing Transactions Exercise

### Test Driven Exercise

The actual signing of the transaction is the trickiest part. Thankfully, we know how to get the sighash, or the z. We have to have the private key to actually sign the transaction and signing the `z` to produce the `r` and `s` of the signature is exactly what we need to do.


```python
from ecc import PrivateKey
from tx import Tx, TxIn, TxOut
from helper import (
    decode_base58,
    p2pkh_script,
    SIGHASH_ALL
)

class Tx(Tx):


    def sign_input(self, input_index, private_key, hash_type):
        '''Signs the input using the private key'''
        # get the sig hash (z)
        z = self.sig_hash(input_index, hash_type)
        # get der signature of z from private key
        der = private_key.sign(z).der()
        # append the hash_type to der (use hash_type.to_bytes(1, 'big'))
        sig = der + bytes([hash_type])
        # calculate the sec
        sec = private_key.point.sec()
        # initialize a new script with [sig, sec] as the elements
        script_sig = Script([sig, sec])
        # change input's script_sig to new script
        self.tx_ins[input_index].script_sig = script_sig
        # return whether sig is valid using self.verify_input
        return self.verify_input(input_index)
```
