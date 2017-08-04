# How to make an altcoin

Okay, I’m going to preface this article by asking you two things:

**First, WHY do you want to create an altcoin?**

This is a starting point.  If you don’t have some original ideas, and the ability to actually read and write C++ code to implement them, then you’re going to create something that can’t do anything Bitcoin can’t do, and in that case there’s no point in releasing it.  So don’t.

If you want to release an altcoin, pump it, get a lot of suckers to invest their money, bribe a cryptocurrency exchange to put up a trading market in it, and then dump a bunch of premined coins on the inflated market and leave all the suckers holding the bag, then “don’t” isn’t quite all the advice I want to give you.  Additionally, “go die in a fire”.

So. If you have a reason to create an altcoin – something you’ve thought of a way to do that won’t ever get into Bitcoin itself and which would enable your ideal cryptocurrency to function in ways Bitcoin can’t – then I have a second question for you.

**Can you commit yourself to maintaining a release?**

This is serious. If you’re going to release an altcoin, you’re going to have a forty-hour-a-week job supporting it. You’re going to have to spend time every day just reading the code to make sure you know what every part of it does. And you’ll need that knowledge, because you’re going to have to figure out technical problems and make technical fixes, in emergency situations, when some joker attacks your block chain. Bitcoin has some stability because Bitcoin has a majority of the hashing power on the planet devoted to it. Your altcoin isn’t even a fart in a hurricane yet, and your proof-of-work block chain will be subject to attacks that you’re going to have to either anticipate or counter in real time. Reading code every day is preparation. The only way you can find your way around it fast enough to make fixes is if you know it inside and out. Anyway, you can have a full time job, or a social and family life, and maintain a released altcoin – but probably not both, unless you’re one of those people who regards sleep as a wholly inadequate time-wasting substitute for caffeine.

Now, with all that said, this article will cover only the beginning of the process: shamelessly cloning an existing coin. Whatever innovations you intend to make, I can’t anticipate them, so there’s no guide here to making them. You have to read the code until you understand it well enough to make them yourself. For purposes of this article, we’re going to use Bitcoin’s 0.10 source code, we’re going to make a near-clone of it named “Newcoin”, and I’m going to be working with Debian Jessie.

**Get the Source Code**
Open up a command line shell. Make a new directory in your home directory named src. It’s where you’ll be working. So your first three command lines are

	cd
	mkdir src
	cd src
Now use your web browser to go to https://bitcoin.org/en/download, find the button in the lower right corner of the box that says “source code”, and get the archive. Its name should be bitcoin-0.10.0(1).tar.gz. Copy it into your new directory and unpack it using the commands:

	cp ~/Downloads/bitcoin-0.10.0\(1\).tar.gz .
	tar xvf bitcoin-0.10.0\(1\).tar.gz
	cp -r bitcoin-0.10.0 newcoin
In the above two commands, the backslashes before the parens are because there are actual parentheses in the filename, and if you just type them, your shell will try to interpret the command differently. And the directory ~/Downloads is where stuff lands on my system when I download it with my web browser. If you’ve got yours set up differently, you should adjust the command to use your download directory instead, whatever it’s named.

And the last command makes a clone of the bitcoin-0.10.0 directory named newcoin – with its own copies of all the files you just extracted. You should use whatever you want thename of your new altcoin to be for the name of this directory.

**Get the stuff the code depends on**
Next, you need to get all the stuff that building Bitcoin depends on. Here’s a sequence of commands to do that.

	su
	echo 'deb-src ftp://ftp.us.debian.org/debian/ sid main contrib non-free' >> /etc/apt/sources.list
	apt-get update
	apt-get build-dep bitcoin
	apt-get –install-recommends install libbitcoin-dev
	exit
Now, I cheated a little bit here. Debian has a Bitcoin package available in its ‘sid’ distribution, but it doesn’t use the same version of the source code we want to work with. The first line makes you root, because root has the permission to modify sources.list and install packages. It’ll ask for the root password and you need to enter it. The second line changes sources.list to add the sid sources to what’s available to your apt command. The third line tells Debian to download all the meta-information about those sources, so it knows what the build process for these packages require. The fourth and fifth lines tell it to download and install all the stuff you would need to build the sid Bitcoin source code package. But you didn’t download the sid source code package, you downloaded the new source code directly from the site instead. The cheat is that the dependencies are the same, so this is an easy way to get everything you need to build. And the last line is to quit being root.

**Test build to make sure your setup works**
The next step is build Bitcoin just to make sure you got everything. So let’s do that. Here are some more command lines. Some of these may take a few minutes to run to completion; this is normal.

	cd bitcoin-0.10.0
	aclocal
	automake --add-missing
	./configure --with-incompatible-bdb
	make
**How all That Worked (The Automake System)**
aclocal generates a file called ‘aclocal.m4′ which is a bunch of macro definitions and so on that automake works with. There was already an aclocal.m4 in the directory, but it is for a version of automake that probably isn’t the one installed on your Jessie system.

automake –add-missing invokes automake, which uses those macros and the file Makefile.am, which is already there, to create another file named Makefile.in. The –add-missing argument tells it to create a couple of standard macro packages that automake uses, because the Bitcoin sources didn’t provide them; it didn’t need to provide them, because it uses the defaults provided by –add-missing.

Makefile.in specifies what configuration needs to know and do and saves that in a shell script named configure. Then ./configure runs that shell script, which tests a lot of things on your system, finds what’s available to build with and link against, and generally figures out in detail how to build the Bitcoin sources it’s looking at, saving that information in a makefile. The –with-incompatible-bdb argument says it’s okay with you to use a version of the Berkeley database later than the one the original Bitcoin client was built with.

Finally make actually uses the makefile that you just built using ./configure, to build the Bitcoin project.

And if you got all the stuff you needed to build with, the whole process should finish without an error.

**Make sure your build worked **
Here’s a couple more command lines to make sure that the build worked, by checking to make sure the executable files it was supposed to build are actually there.

	ls src/bitcoind
	ls src/bitcoin-cli
	ls src/qt/bitcoin-qt
Got ’em? Excellent! That means your build environment has everything you need to work with to make an altcoin. So, now that the smoke test is over, it’s time to actually go to the newcoin directory and start making your altcoin.

**Make Your Altcoin Use Its Own Name**
So here are some more command lines:

	cd ~/src/newcoin
	find . -type f -print0 | xargs -0 sed -i 's/bitcoin/newcoin/g'
	find . -type f -print0 | xargs -0 sed -i 's/Bitcoin/Newcoin/g'
	find . -type f -print0 | xargs -0 sed -i 's/BitCoin/Newcoin/g'
	find . -type f -print0 | xargs -0 sed -i 's/BITCOIN/NEWCOIN/g'
	find . -type f -print0 | xargs -0 sed -i 's/BTC/NCN/g'
	find . -type f -print0 | xargs -0 sed -i 's/btc/NCN/g'
	find . -type f -print0 | xargs -0 sed -i 's/Btc/NCN/g'
The first four commands change every occurrence of the string ‘bitcoin’ in four capitalizations to the string ‘newcoin’ in all the files in this directory or under it, in three different capitalizations. The second three commands do the same thing to the string ‘btc’, transforming every instance of it into all-caps.

**Rename ‘bitcoin’ filenames to your altcoin’s name**
Now, because this also affected makefiles which contain filenames, and affected source files which have filenames in their include statements, you’ve got to change all the filenames that have the string ‘bitcoin’ in them to match what’s now in the include statements and makefiles. Fortunately, this is just as easy.

	find . -exec rename 's/bitcoin/newcoin/' {} ";"
	find . -exec rename 's/btc/NCN/' {} ";"
Will rename every file that has ‘bitcoin’ or ‘btc’ in the name, recursing into subdirectories as needed. So now your filenames match up with your changed makefiles and include statements.

**Check For Stuff Your Auto-edits Missed Because It Was Misspelled**
Now look for misspellings of it.

	grep -ir bitc
Aside from uncovering a bunch of references in source code to nDebitCached and a few similarly named variables, reveals that in Arabic translations ‘Bitcoin’ was rendered as ‘Bitcion’ in two translated sentences. In a language I don’t recognize at all but which may be Estonian, it became ‘Bitconi’ once, and the Dutch translation mentions a “bitcon:-URI.’

The last is certainly a misspelling, because URI’s are spelled the same regardless of language. So

	sed -i 's/bitcon/newcoin/' src/qt/locale/newcoin_da.ts
sorts that.

I remember about Arabic that it inflects words using patterns of triple vowels, and ‘bitcion’ is used more than once, so my first thought is that it might not be a misspelling. A quick check on Google, however, reveals a lot of instances of ‘Bitcoin’ misspelled in English, and no Arabic pages. So with more evidence, I think I’ll conclude that it probably is a misspelling – about which, again, there’s a chance I’m wrong, and you should write on your list of things to do asking someone who actually speaks Arabic about it. But for now patch it using

	sed -i 's/bitcion/newcoin/' src/qt/locale/newcoin_ar.ts
And then there’s the Estonian-or-whatever-it-is. I haven’t heard of any languages outside the middle east that inflect by modifying vowel sequences, and ‘Bitconi’ seems likely to be a misspelling, so once more I’ll just make a possibly-dumb assumption and patch it.

	sed -i 's/Bitconi/Newcoin/' src/qt/locale/newcoin_et.ts
**Fix The Copyright Notices**
Now, the license under which you have access to this code mentions, among other things, leaving copyright notices intact – and you mangled ’em when you switched ‘Bitcoin’ to ‘Newcoin’ because the boilerplate line there refers to the ‘Bitcoin Development Team’. So you need to change ‘Newcoin’ back to ‘Bitcoin’ but only on the lines where it’s part of a copyright statement.

	find . -type f -print0 | xargs -0 sed -i '/opyright/ s/Newcoin/Bitcoin/'
sorts that. I left the first letter off of ‘Copyright’ because I didn’t want it to care about the capitalization. You’ll be adding your own copyright notices to these files, but only when you do significant edits.

There were also a lot of references to Bitcoin in the release-notes files, and it would be an outright lie to pretend that those earlier releases were releases of your alt rather than releases of Bitcoin, so those need changed back, too.

	sed -i 's/newcoin/bitcoin/g' doc/release-notes/*
	sed -i 's/Newcoin/Bitcoin/g' doc/release-notes/*
handles that.

**Change the Ports**
Bitcoin uses ports 8332 and 18332 on its main net and 8333 and 18333 on its test net. You want newcoin to use different ports, just to make it handy for people to run both clients at the same time.

	find . -type f -print0 | xargs -0 sed -i 's/8332/9443/'
	find . -type f -print0 | xargs -0 sed -i 's/8333/9444/'
will switch newcoin to using ports 9443 and 19443 on mainnet and 9444 and 19444 on testnet. Once again, you should provide your own values. But don’t change any of the port numbers to anything below 1000, or nobody except root will be able to run it. I should mention here that in addition to the port numbers, you just changed a couple of hex strings in the testing directory that the test framework feeds to the RPC interface. But you were going to fail those tests anyway, because other edits you’re making will make Newcoin keys different from Bitcoin keys.

**Change the Graphics**
You don’t really want to show Bitcoin’s icons and images every time you start up, so you need to go and edit some graphics in the directory src/qt/res. When editing, be sure to save new images that are exactly the same size and file format as the old ones. When you want to change the sizes and/or file formats, you need to understand the QT framework first.

I don’t have any nifty command lines that can help you make good art, so you’re on your own here. At the very least, you’re going to want to edit newcoin.ico, newcoin.png, newcoin_testnet.ico, newcoin_testnet.png, and newcoin.icns. I suggest using GIMP for all of these edits; it speaks all those graphics formats. The ico and icns files are particularly annoying to deal with because you have to make similar (but not identical) changes on many different layers of the saved image.

**Build Your Altcoin For The First Time**
Now we’re done with wholesale edits. It’s time to see whether you broke anything yet. So type

	aclocal;automake --add-missing;./configure --with-incompatible-bdb;make
and wait a few minutes.

Did it build? You now have newcoind, newcoin-cli, and newcoin-qt? Awesome! So now that we managed to get the wholesale edits right, it’s time to actually look at source code and start doing some very specific edits.

**In chainparams.cpp, Edit The Checkpoints**
Get your favorite programming editor, go to the newcoin/src/ directory, and open up chainparams.cpp.

Search for the string ‘mapCheckpoints’ and it will take you immediately to line 55, which sets a static variable named mapCheckpoints to a list of pairs – the first member of each pair is the block height, and the second is the hash of that block. The Bitcoin client uses these as it verifies the Bitcoin block chain. So if you leave those in, the newcoin client will be looking for blocks that don’t exist in the newcoin block chain. So get rid of ’em, and put a place holder for your genesis block in instead. This place holder will be wrong, but we can’t fix it until after we mine a genesis block. After your edit, it should look like this:

	static Checkpoints::MapCheckpoints mapCheckpoints =
	boost::assign::map_list_of
	( 0, uint256("0x001"));
Zero is the block height of the genesis block, and uint256(“0x001″) is a wildly wrong guess about what the hash is going to be.

Make the same edit at the locations where it sets ‘mapCheckpointsTestnet’ and ‘mapCheckpointsRegtest’.

**Edit The Checkpoint Data**
Right under each of these edited checkpoint lists, there are blocks that initialize a corresponding variable. These variables are named data (for the main network), dataTestnet (for the test network), and dataRegtest (for regression testing). Go to the command line and type

	date +%s
to find out what the current unix epoch date is, then overwrite the first number in each of these blocks with the current date. You’ll have to do this again (and mine new genesis blocks) right before launch. The second number in each block is a transaction count as of the most recent checkpoint; it should be replaced by zero, because there are no transactions before the genesis block. The third number is the number of transactions per day you expect after the last checkpoint. It doesn’t matter as much, but for now I suggest 500 for the main network, 250 for the test network, and zero for the regtest. Updating these numbers as the amount of traffic on your coin’s block chain changes is one of the things you do as dev, if you want your clients to make accurate estimates of their progress as they reindex or verify the block chain.

**Change The Protocol ‘Magic’ Bytes**
The next block after dataRegtest is setting another variable named CMainParams. And you have a lot of things to change here. The first four lines of code set values in an array called pchMessageStart. These are the network ‘magic bytes'; They’re a series of four bytes that identify messages as belonging to a particular protocol. 0xf9, 0xbe, 0xb4, and 0xd9 are the ones Bitcoin uses. You want to use something else, so that nobody can ever trick your newcoin client into connecting to the Bitcoin network, or vice versa. It doesn’t matter what these are except that they shouldn’t match the ones used in other protocols (particularly other cryptocurrency block chains). They have to be values between 0 and 255. I suggest going back to the shell and using

	echo $RANDOM
Just repeat the command, and write down the last three digits whenever they’re under 255. When you’ve got four of them, set the pchMessageStart values in CMainParams. When you’ve got four more, set the pchMessageStart values in CTestNetParams. And when you’ve got four more, set the pchMessageStart values in CRegTestParams.

**Make New Alert And Genesis Coinbase Keys**
The next line (in the first two params objects) sets vAlertPubKey values. So go back to the command line to get some.

	openssl ecparam -genkey -name secp256k1 -out alertkey.pem
	openssl ec -in alertkey.pem -text > alertkey.hex
	openssl ecparam -genkey -name secp256k1 -out testnetalert.pem
	openssl ec -in testnetalert.pem -text > testnetalert.hex
	openssl ecparam -genkey -name secp256k1 -out genesiscoinbase.pem
	openssl ec -in genesiscoinbase.pem -text > genesiscoinbase.hex
Will create private keys and drop them into files named alertkey.pem, testnetalert.pem, and genesiscoinbase.pem, then expand them into key pairs and drop the key pairs in hex format into files named alertkey.hex, testnetalert.hex, and genesiscoinbase.hex.

**Insert Your New Alert Keys**

	cat alertkey.hex
Will show the contents of alertkey.hex. Copy the four lines that appear between ‘pub’ and ‘ASN1 OID: secp256k1′ and paste them into the source code file replacing the string contents in the line that says vAlertPubKey = ParseHex(“…”);

Then edit to strip the colon characters and linefeeds out of it, and you’ve got an alert key. If you ever need to send an alert out over the network, you can use the corresponding private key.

	cat testnetalert.hex
will show you a key for testnet, so you can set the vAlertPubKey for the CTestNetParams block. The CRegTest block doesn’t have one of its own; if it ever needs one, it uses the testnet key.

**Edit The Timestamp String**
Now we skip down to the famous timestamp string, at the line

	const char* pszTimestamp = "The Times 03/Jan/2009 Chancellor on brink of second bailout for banks";
It’s traditional to pick a newspaper headline for the day of launch, but you don’t have to. Whatever you use, keep it short. If it’s over 90 characters or so the block will fail a length check that’s supposed to prevent denial-of-service attacks from people attaching big data to transactions.

**Set The Genesis Coinbase Key**

	cat genesiscoinbase.hex
will show you a key for the genesis coinbase transaction, so you can paste that into the CMainParams block replacing the string contents in the line

	txNew.vout[0].scriptPubKey = CScript() << ParseHex("...") << OP_CHECKSIG;
**Set the Genesis Time**
Going down a little more, change the line in the CMainParams block that sets genesis.nTime so that it sets it to the same Unix epoch date you got using the ‘date’ command above. Repeat that edit in the CTestNetParams block.

**Delete References To Bitcoin’s Seed Nodes**
Skipping down past the hashGenesisBlock stuff, you’ll see a series of lines that set values for a list named vSeeds. These are network addresses where long-lived Bitcoin nodes are supposed to be found, so that people can always connect to the Bitcoin network. Which, as you’re establishing now, is not your network. Until you put up servers to do that for your new network, replace these lines (in both CMainParams and CTestNetParams) with the simple statements:

	vFixedSeeds.clear();
	vSeeds.clear();
**Change The Key Prefixes**
Moving on down, we get to a block of statements that set values in an array of lists named base58Prefixes. These are prefix bytes that are added to the keys. By setting them to different values, you can assure that keys for coins on Newcoin’s network will never be valid Bitcoin keys and vice versa. To a certain extent, setting these numbers also determines what characters the base58 form of the keys that users see starts with. The chances of a random collision in keys are ridiculous, but “chances” and “random” are quaint old-fashioned ideas here; you’re working on code that somebody is going to deliberately attack. So change the values. You can use echo $RANDOM at the command line again if you want. If you care about what initial characters your choices will result in for the base58 form of the keys, consult the list at the Bitcoin wiki.

There is one important constraint here: none of the lists can be identical, because the clients look at the initial bytes to figure out what kind of key they’re dealing with. Also, don’t change the length of any list until you’ve read the code enough to know everything that will affect.

base58Prefixes(PUBKEY_ADDRESS) is the value prefixed to addresses that consist of public keys – these are the public half of a pubkey/private key pair that respectively represent a txOut and the ability to spend it. base58Prefixes(SECRET_KEY), predictably, are the prefixes for the private half of the pair. This is the key your client keeps in its wallet to spend a txOut. base58Prefixes(SCRIPT_ADDRESS) is affixed to addresses that are the hashes of scripts; looking for the difference between the initial bytes is how the client knows which of the two “default” spend scripts to apply. Finally, base58Prefixes(EXT_PUBLIC_KEY) and base58Prefixes(EXT_SECRET_KEY) are four-byte prefixes for the so-called “stealth addresses” that got introduced in Bitcoin version 0.9, which allow fun things like addresses that can be used to generate new keys that can receive payments but not spend them. These two should have the same first byte but must differ in the other three bytes.

Aaaanyway, it doesn’t matter what you change them to, it just matters that they don’t match anything they might ever be confused with, like Bitcoin keys or each other. So give them new values in CMainParams, CTestNetParams, and CRegTestParams.

**Add Code To Mine A Genesis Block**
Finally, we’re just about ready to mine a Genesis block. But the code to actually mine a Genesis block hasn’t been in Bitcoin for a long time; that block is already mined. Its parameters are there to check against, but there’s no code to establish what some of them ought to be. You need to fix that. So in the CMainParams initializer, right after

	genesis.nNonce = 414098458;
and before

	hashGenesisBlock = genesis.GetHash();
	assert(hashGenesisBlock == uint256("....."));
	assert(genesis.hashMerkleRoot == uint256("....."));
paste this code:

	hashGenesisBlock = uint256("0x01")
	if (true && genesis.GetHash() != hashGenesisBlock)
			{
				Logprintf("recalculating params for mainnet.\n");
				Logprintf("old mainnet genesis nonce: %s\n", genesis.nNonce.ToString().c_str());
				Logprintf("old mainnet genesis hash:  %s\n", hashGenesisBlock.ToString().c_str());
				// deliberately empty for loop finds nonce value.
				for(genesis.nNonce == 0; genesis.GetHash() > bnProofOfWorkLimit; genesis.nNonce++){ } 
				Logprintf("new mainnet genesis merkle root: %s\n", genesis.hashMerkleRoot.ToString().c_str());
				Logprintf("new mainnet genesis nonce: %s\n", genesis.nNonce.ToString().c_str());
				Logprintf("new mainnet genesis hash: %s\n", genesis.GetHash().ToString().c_str());
			}
Use the same code in the CTestNetParams initializer, except change ‘mainnet’ into ‘testnet.’ Likewise the CRegTestParams initializer, except change ‘mainnet’ into ‘regtest’.

Now build again. This time you have already done all the autoconf stuff, so you just need to type

	make
and you only changed one file, which isn’t included anywhere else. So it should be built in a few seconds.

**Now Mine Your Genesis Blocks**
Got all that? Now run newcoin-qt.

It will throw up a dialog box asking where to install. Let it have the default it wants, which will be a new subdirectory of your home directory named ~/.newcoin.

Then Nothing will happen, maybe for up to an hour. Maybe longer than an hour, depending on your dumb luck and the speed of your computer. If your computer’s slow, this may be a good time to go get a sandwich.

Here’s what’s going on. The program set the nonce, hashed the genesis block, noticed that the new hash didn’t match 0x01, and is now living in that tight little empty for loop, incrementing the nonce, hashing the block, and checking to see whether the new hash is low enough to meet proof-of-work requirements.

After a while, it will die. Meaning, it found a nonce that gave the genesis block a hash low enough to pass proof-of-work, got out of the for loop, logged the new hash and nonce and the Merkle tree value, and then hit the assert that still insists on the hash value being Bitcoin’s genesis block hash value. You need the new values it logged. Type

	tail ~/.newcoin/debug.log
and you’ll see the last few lines of logfile output.

Now you have a nonce and a hash and a Merkle root so go change the necessary lines in the CMainNet initializer.

In the code you pasted in, you’ve initialized hashGenesisBlock to be uint256(“0x01″). Replace the string value with the new genesis hash.

Right above the code you pasted in, you’ll see a line that sets a value for genesis.nNonce. Change it to your new nonce value.

Right below the code you pasted in, you’ll find assert() statements that check the value of the Merkle root and the hash. Change those to assert your new Merkle root and genesis Hash values.

Near the top of the file, there’s your mapCheckpoints list, claiming that the hash of the genesis block is 0x01. Change that to your new hash value.

Once you’ve made these edits, recompile and run it again. The same thing will happen all over again, but this time for testnet. And you change the same things in the CTestNetParams initializer, etc, that you changed in the CMainParams initializer.

Recompile and run it again, and it will trip on the regtest values. Fortunately these won’t take any time because the regtest proof-of-work is much easier. Change the same things in the CRegTestParams initializer.

**Now STOP Mining Genesis Blocks**
Okay? Now you have mined your new genesis blocks. Now, in the code you pasted in, see where the stupid-looking if condition checks for

	if (true && genesis.GetHash() != hashGenesisBlock)
?
Change those three ‘true’s to ‘false’s. The purpose of this ‘true/false’ business is to make it easy for you to switch back and forth from mining genesis blocks to not mining genesis blocks. You’ll need to mine a new genesis block right before launch, and may need several more during testing and development depending on whether you do anything that changes the block headers or initial coinbase transaction (like changing the number of coins in it). But in the client you actually test and release, you don’t want genesis block mining turned on. If it sees a wrong genesis block, you want it to bomb out immediately, not sit there trying to create a new one.

Whenever you need to mine new genesis blocks because you changed something that invalidated the old one during development, you will come back to chainparams.cpp, update the date parameters and timestamp string, make the three true/false edits, recompile, do

	rm -rf ~/.newcoin
so it doesn’t see the (now) wrong former genesis block in the data there, and run it.

**What You’ve Got Now**
At this point you have created a complete, and completely simple, clone of Bitcoin version 0.10. If this is all you’re doing, then it’s been a nice learning experience but don’t launch this coin. Unless doing something new, there is no point.

**Problems You Still Need To Solve**
This thing you have created cannot yet possibly survive as an altcoin; its difficulty adjustment algorithm will not respond quickly enough to prevent your block chain from getting instamined, and then stuck. Mining whales will scoop up two weeks of coins while processing almost no transactions, in less than an hour while your block chain is profitable, then when the difficulty adjustment for that two weeks hits they’ll leave your chain high and dry. You won’t get a block for days and nobody will be able to use your alt to make any kind of transaction because of it. The result is a block chain that can handle transactions for maybe two hours out of every year, which is useless to everyone.

So the next thing you need to do is go fix that. And when you’ve fixed it, your new alt still won’t be capable of surviving; First, you don’t have any network seeds up, and second everybody has one fix or another for the difficulty problem that a cloned coin creates. Once again, you either have a new idea and the ability to develop and support it, or you don’t have anything that’s worthwhile to launch. What I have shown you here are the first few steps on a long road.

I’ll talk about one way to fix it in the next installment.

原文链接：http://dillingers.com/blog/2015/04/18/how-to-make-an-altcoin/
修正多处作者笔误
