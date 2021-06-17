.. |br| raw:: html

   <br />

Securing your data
==================

.. contents:: Table of Contents
   :depth: 3


Encryption can be used to protect datasets, generated signatures and models. Two options exists, either a one-factor encryption that is linked
to the CPSign license or a two-factor encryption where the license is combined with a physical security token (CPSign currently uses YubiKeys_) to secure
the encrypted data. 

.. _YubiKeys: https://www.yubico.com/


One-factor Encryption
^^^^^^^^^^^^^^^^^^^^^
PRO and Predict licenses can be generated to give access to encryption functionality in CPSign. Encrypted data (precomputed sparse files, signatures or models)
will be linked to the given license and cannot be accessed without that specific license in the future. CPSign uses AES (Advanced Encryption Standard) to secure
data. If you choose to encrypt data to a specific Predict license, it will no longer be possible to get anything back into plain (decrypted) state. The
Predict license will only give access to decrypt the data internally to make predictions. 


Two-factor Encryption
^^^^^^^^^^^^^^^^^^^^^
Two-factor encryption adds to the security of only having a license tied to the encryption. After all, data can be hacked or handled in incorrect ways and fall into the
hands of a perpetrator, the perpetrator could then use CPSign for making their own predictions using your chemistry. Encrypted data can however not be decrypted into
plain text and accessed straight away, but only used for predictions (given that encryption is tied to a Predict license). Further requiring a physical security token
forces a perpetrator to get access to data, license, security token and CPSign to have access to making their own predictions, securing your data much better.
 

"Three-factor" Encryption
^^^^^^^^^^^^^^^^^^^^^^^^^
On top of the two-factor encryption described above, we can also configure YubiKeys with a unique 4-9 characters long PIN number and thus adding further
security. 