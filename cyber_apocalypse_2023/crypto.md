# crypto

##  Ancient Encodings 
> Solved by Legend

Challenge description


> Your initialization sequence requires loading various programs to gain the necessary knowledge and skills for your journey. Your first task is to learn the ancient encodings used by the aliens in their communication.

In this challenge we were provided a `python` script named `source.py` and `output.txt` file containing the encrypted flag.

```python
# source.py
from Crypto.Util.number import bytes_to_long
from base64 import b64encode

FLAG = b"HTB{??????????}"


def encode(message):
    return hex(bytes_to_long(b64encode(message)))


def main():
    encoded_flag = encode(FLAG)
    with open("output.txt", "w") as f:
        f.write(encoded_flag)


if __name__ == "__main__":
    main()
```

```
# output.txt
0x53465243657a467558336b7764584a66616a4231636d347a655639354d48566664326b786246397a5a544e66644767784e56396c626d4d775a4446755a334e665a58597a636e6c33614756794d33303d
```

The script is encoding the flag in `hex --> base64` to encrypt the flag.

To solve this I used CyberChef with following steps

![](https://i.imgur.com/xJzvIcV.png)


----------

## Perfect Synchronization
> Solved by Avantika(@iamavu) 

Challenge Description

> The final stage of your initialization sequence is mastering cutting-edge technology tools that can be life-changing. One of these tools is quipqiup, an automated tool for frequency analysis and breaking substitution ciphers. This is the ultimate challenge, simulating the use of AES encryption to protect a message. Can you break it?

We are given two files, one is very long hex text file and another one is the below is the `source.py`

```python
#import library and the plaintext(flag)
from os import urandom 
from Crypto.Cipher import AES
from secret import MESSAGE

#make all characters from the message in uppercase except underscore, curly braces and space
assert all([x.isupper() or x in '{_} ' for x in MESSAGE])


class Cipher:

    def __init__(self):
        self.salt = urandom(15) #generate secure random salt using 'urandom'
        key = urandom(16) #generate secure random key using 'urandom'
        self.cipher = AES.new(key, AES.MODE_ECB) #generate aes-ecb cipher

    def encrypt(self, message):
        return [self.cipher.encrypt(c.encode() + self.salt) for c in message] #encode the string, add salt and then encrypy it char by char


def main():
    cipher = Cipher()
    encrypted = cipher.encrypt(MESSAGE) #encrypt the plaintext/flag
    encrypted = "\n".join([c.hex() for c in encrypted]) #hex it up char by char with new line after every char

    with open("output.txt", 'w+') as f:
        f.write(encrypted) #write the file to output.txt


if __name__ == "__main__":
    main()
```

P.S - Comments are added by me, challenge didn’t have the comments

So we know what the `source.py` is doing, but here is the catch, if you open up the `output.txt` you will see that lot of the hex strings are being repeated and the description mentions the tool `quipqiup` which is a frequency analysis tool.

My approach to this was that each char would be alphabet excluding the underscore, curly braces and space. So the encryption of let’s say `a` would be the same hex string because that’s how AES-ECB works. 
So if we have default frequency of english alphabet, and we calculate frequency of various hex strings in the `output.txt` we should be able to co-relate which hex string is which character and then pass this new replaced text to `quipqiup` and get our flag
time to script, hackers!

```python
import collections

default_frequency = list("ETAOINSRHDLUCMFYWGPBVKXQJZ _{}") #default frequnece of english alphabet
output = open('output.txt', 'r') 
ct_list = output.readlines() #read the output file line by line
final_list = [item.replace('\n', '') for item in ct_list] #remove the new line and make it a list
frequency = collections.Counter(final_list) #create dict with key/value pair of string and it's frequency
sorted_dict = dict(sorted(dict(frequency).items(), key=lambda x:x[1], reverse=True)) #looks a lot but it essentially sorts the dict by value (frequency)
ultra_dict = dict(zip(sorted_dict, default_frequency)) #create a dict with value replaced with alphabets
text = ''.join(ct_list) #get the ciphertext
for hexvalue, letter in ultra_dict.items():
        text = text.replace(hexvalue, letter) #replace each hext string in cipher text with the letter it belongs to from the final dict we created
print(text.replace('\n', ''))
print('\n')
print("Send above text to quipqiup") 
output.close()
```

Well, that was really messy logic and very bad code but it works : D
We will get output of some text when you run this

    YNTJCTRLFEORODFASAESAEPOATMEHREIUTEYOLIEIUOIESREORFEGSXTREAINTILUEHYEVNSIITREDORGCOGTELTNIOSREDTIITNAEORMELHBPSROISHRAEHYEDTIITNAEHLLCNEVSIUEXONFSRGEYNTJCTRLSTAEBHNTHXTNEIUTNTESAEOELUONOLITNSAISLEMSAINSPCISHREHYEDTIITNAEIUOIESAENHCGUDFEIUTEAOBTEYHNEODBHAIEODDEAOBWDTAEHYEIUOIEDORGCOGTESRELNFWIORODFASAEYNTJCTRLFEORODFASAEODAHEKRHVREOAELHCRISRGEDTIITNAESAEIUTEAICMFEHYEIUTEYNTJCTRLFEHYEDTIITNAEHNEGNHCWAEHYEDTIITNAESREOELSWUTNITZIEIUTEBTIUHMESAECATMEOAEOREOSMEIHEPNTOKSRGELDOAASLODELSWUTNAEYNTJCTRLFEORODFASAENTJCSNTAEHRDFEOEPOASLECRMTNAIORMSRGEHYEIUTEAIOISAISLAEHYEIUTEWDOSRITZIEDORGCOGTEORMEAHBTEWNHPDTBEAHDXSRGEAKSDDAEORMESYEWTNYHNBTMEPFEUORMEIHDTNORLTEYHNETZITRASXTEDTIITNEPHHKKTTWSRGEMCNSRGEVHNDMEVONESSEPHIUEIUTEPNSISAUEORMEIUTEOBTNSLORAENTLNCSITMELHMTPNTOKTNAEPFEWDOLSRGELNHAAVHNMEWCQQDTAESREBO_HNERTVAWOWTNAEORMENCRRSRGELHRITAIAEYHNEVUHELHCDMEAHDXTEIUTBEIUTEYOAITAIEATXTNODEHYEIUTELSWUTNAECATMEPFEIUTEOZSAEWHVTNAEVTNTEPNTOKOPDTECASRGEYNTJCTRLFEORODFASAEYHNETZOBWDTEAHBTEHYEIUTELHRACDONELSWUTNAECATMEPFEIUTE_OWORTATEBTLUORSLODEBTIUHMAEHYEDTIITNELHCRISRGEORMEAIOISAISLODEORODFASAEGTRTNODDFEUIP{O ASBWDT ACPAISICISHR SA VTOK}ELONMEIFWTEBOLUSRTNFEVTNTEYSNAIECATMESREVHNDMEVONESSEWHAASPDFEPFEIUTECAEONBFAEASAEIHMOFEIUTEUONMEVHNKEHYEDTIITNELHCRISRGEORMEORODFASAEUOAEPTTRENTWDOLTMEPFELHBWCITNEAHYIVONTEVUSLUELORELONNFEHCIEACLUEORODFASAESREATLHRMAEVSIUEBHMTNRELHBWCISRGEWHVTNELDOAASLODELSWUTNAEONTECRDSKTDFEIHEWNHXSMTEORFENTODEWNHITLISHREYHNELHRYSMTRISODEMOIOEWCQQDTEWCQQDTEWCQQDT

and when you send this to https://quipqiup.com/ we will get out plaintext

    FREQUENCYZANALYSISZISZBASEDZONZTHEZFACTZTHATZINZANYZGIVENZSTRETCHZOFZWRITTENZLANGUAGEZCERTAINZLETTERSZANDZCOMBINATIONSZOFZLETTERSZOCCURZWITHZVARYINGZFREQUENCIESZMOREOVERZTHEREZISZAZCHARACTERISTICZDISTRIBUTIONZOFZLETTERSZTHATZISZROUGHLYZTHEZSAMEZFORZALMOSTZALLZSAMPLESZOFZTHATZLANGUAGEZINZCRYPTANALYSISZFREQUENCYZANALYSISZALSOZKNOWNZASZCOUNTINGZLETTERSZISZTHEZSTUDYZOFZTHEZFREQUENCYZOFZLETTERSZORZGROUPSZOFZLETTERSZINZAZCIPHERTEXTZTHEZMETHODZISZUSEDZASZANZAIDZTOZBREAKINGZCLASSICALZCIPHERSZFREQUENCYZANALYSISZREQUIRESZONLYZAZBASICZUNDERSTANDINGZOFZTHEZSTATISTICSZOFZTHEZPLAINTEXTZLANGUAGEZANDZSOMEZPROBLEMZSOLVINGZSKILLSZANDZIFZPERFORMEDZBYZHANDZTOLERANCEZFORZEXTENSIVEZLETTERZBOOKKEEPINGZDURINGZWORLDZWARZIIZBOTHZTHEZBRITISHZANDZTHEZAMERICANSZRECRUITEDZCODEBREAKERSZBYZPLACINGZCROSSWORDZPUJJLESZINZMA_ORZNEWSPAPERSZANDZRUNNINGZCONTESTSZFORZWHOZCOULDZSOLVEZTHEMZTHEZFASTESTZSEVERALZOFZTHEZCIPHERSZUSEDZBYZTHEZAXISZPOWERSZWEREZBREAKABLEZUSINGZFREQUENCYZANALYSISZFORZEXAMPLEZSOMEZOFZTHEZCONSULARZCIPHERSZUSEDZBYZTHEZ_APANESEZMECHANICALZMETHODSZOFZLETTERZCOUNTINGZANDZSTATISTICALZANALYSISZGENERALLYZHTB{A SIMPLE SUBSTITUTION IS WEAK}ZCARDZTYPEZMACHINERYZWEREZFIRSTZUSEDZINZWORLDZWARZIIZPOSSIBLYZBYZTHEZUSZARMYSZSISZTODAYZTHEZHARDZWORKZOFZLETTERZCOUNTINGZANDZANALYSISZHASZBEENZREPLACEDZBYZCOMPUTERZSOFTWAREZWHICHZCANZCARRYZOUTZSUCHZANALYSISZINZSECONDSZWITHZMODERNZCOMPUTINGZPOWERZCLASSICALZCIPHERSZAREZUNLIKELYZTOZPROVIDEZANYZREALZPROTECTIONZFORZCONFIDENTIALZDATAZPUJJLEZPUJJLEZPUJJLE

Which is sorta broken as `Z` came in the place of `space` and `space` came in the place of `_` but it sorta worked(?)
we have our flag string
`HTB{A SIMPLE SUBSTITUTION IS WEAK}`
replace space by underscore and we good to go
`HTB{A_SIMPLE_SUBSTITUTION_IS_WEAK}`


----------
## Small StEps
> Solved by Starry-Lord 


![challenge description](https://paper-attachments.dropboxusercontent.com/s_79E00D07E4DEC37B8D578FB2859311AD52232A853C026AF85C58F0A942AC33F2_1679589191167_small_steps.png)



    ──(starlord㉿HAL-9090)-[~/Bureau/tools/RsaCtfTool]
    └─$ python3 RsaCtfTool.py -n 5597972584718598406690499051990413577290211490538759507986683212737503437232726430145509927989660577399319692954532703407466611677039848415663500281956971 -e 3 --uncipher 70407336670535933819674104208890254240063781538460394662998902860952366439176467447947737680952277637330523818962104685553250402512989897886053


    Results for /tmp/tmp3441ap7k:
    
    Unciphered data :
    HEX : 0x4854427b356d61316c5f452d7870306e336e747d
    INT (big endian) : 412926389432612660984016953290834154417829082237
    INT (little endian) : 716220332648329290345370113693957684850803758152
    utf-8 : HTB{5ma1l_E-xp0n3nt}
    utf-16 : 呈筂洵ㅡ彬ⵅ灸渰渳絴
    STR : b'HTB{5ma1l_E-xp0n3nt}'
    

