# DHKE密鑰交換

<procedure id="1procedure0">
<step>
    Alice and Bob both agree the number p = 13, g = 7
</step>
<step>
    Alice choose n = 2, compute q = g^n mod p = 7^2 mod 13 = 10
</step>
<step>
    Bob choose m = 8, compute r = g^m mod p = 7^8 mod 13 = 3
</step>
<step>
    Alice receive r = 3, compute r^n mod p = 9
</step>
<step>
    Bob receive q = 10, compute 10^8 mod 13 = 9
</step>
<step>
    Our private symmetric key = 9
</step>
</procedure>