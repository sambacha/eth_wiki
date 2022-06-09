The latest TurboEthereum bundle (webthree-umbrella) now supports the loading of published ÐApps. This is a quick tutorial on how to publish your ÐApp.

Let's assume you have a super-simple two file ÐApp composed of `dapp.html` and `dapp.sol`:

```
//dapp.sol

contract Counter
{
event NewBest(bytes32 what);
function like(bytes32 what) { if (++count[what] > count[best]) { best = what; NewBest(what); } }
mapping ( bytes32 => int ) public count;
bytes32 public best;
}
```

Let's assume you've deployed `dapp.sol` to the network and it has address `XE04GJUKPKOU669P92W3QG0WCVCJ4JOYVD` (yes, that's an ICAP address - they're now supported transparently whereever old-style dangerous raw-hex addresses were).

```
<!--dapp.html-->
<html>
<head>
<script>
web3;
var Counter = web3.eth.contract([{"constant":true,"inputs":[{"name":"","type":"bytes32"}],"name":"count","outputs":[{"name":"","type":"int256"}],"type":"function"},{"constant":false,"inputs":[{"name":"what","type":"bytes32"}],"name":"like","outputs":[],"type":"function"},{"constant":true,"inputs":[],"name":"best","outputs":[{"name":"","type":"bytes32"}],"type":"function"},{"anonymous":false,"inputs":[{"indexed":false,"name":"what","type":"bytes32"}],"name":"NewBest","type":"event"}]);
var theCounter = Counter.at("XE04GJUKPKOU669P92W3QG0WCVCJ4JOYVD")
</script>
</head>
<body>
<h1 id=”best”></h1>with
<h2 id=”bestcount”></h2>
<button onclick=”theCounter.like(‘Cake’)”>Cake!</button>
<button onclick=”theCounter.like(‘Reggae’)”>Reggae!</button>
<script>
l = document.getElementById(‘leader’)
lc = document.getElementById(‘leadercount’)
theCounter.NewBest(function(e, r) {
    l.innerHTML = r._what;
    lc.innerHTML = theCounter.count(r._what);
});
</script>
</body>
</html>
```

There's the front-end. We'll assume it's a single file for now, but of course we can support Ðapps of arbitrary complexity and size.

Notice that it initialises the contract object with the right address (`theCounter = Counter.at...`) and also notice that it has a funny line mentioning the `web3` object (`web3;`). There is magic afoot that will guarantee that where ever such a line is given, the "real" `web3` object will appear come the time of viewing.

Our deployment process (which will make the Ðapp work on anyone's AlethZero or AlethFive browser, and eventually under Mist, too) is a four-step process:

- Assemble the bundle
- Upload the bundle somewhere
- Register the URL hint
- Register the ÐApp name

### Assemble the bundle

Assembling the bundle is basically just building a zip-like chunk of data that contains all of our front-end assets, together with a manifest file which describes how the assets are arranged from the point of view of a "virtual" web server. For this job, we use the `rlp` command and the `assemble` function.

Assuming we're in a directory with our two files, here's what we'd run:

```
rlp assemble --dapp . dapp.html,path:/
```

The three arguments refer to:
- `--dapp` We wish to create an encrypted bundle for a ÐApp and output the base-64 content;
- `.` The root directory for determining relative paths to the bundle's files (alternatively, this can be a special [manifest](link_todo) file);
- `dapp.html,path:/` The file name `dapp.html` followed by the corresponding manifest's arguments, in this case we're giving it a path of `/`, denoting that it is the primary html page to be served. We can do things like set the content-type or error code (for this example, the defaults will suffice). For complex projects, you'll want to make a manifest file.

It will output something like this:

```
Keccak of RLP: a8cede4d755c67e60566895bdf6b60482191ded942ff448f72138b8ba9f1c189
BGgS/vtQ8fWxyLxrpSioLFctnnGelal2rvCbCoKaRj1Ktb/51yW9hFdz6uh7OVTC3HK0J5V7QSOObZV+VHEC7N7RA1FriF+fDEnEuZ/BtwoME3frz9nNgx/b3cdfKt9Fw6ZDaVQJala84Czf8YvcK4RGk4x1Sswtt38HQHQeNptGhRT4OL2SmQ+G1HOpC5y/R8sSfgPV6khJK7v1u81J+IToWeKPiSMEshbn2AzoDq+/HPemnkKPdryKUwIx4ph3mpwiLYjFiLvy0EvGjh+XNdNOeAr4nDyVFRMQw5Sfb5zGgKSszEriS+pUXqLplrFsblbwdYWIBbLrI0B2ydi0HTxz0tsNR8pOzDG9V/v3qE8HNxJ4wtTW6xfjFCJ7orUZWOPjYSdCtVvMZ3Q2RGFYUiKK3POVdNl1WKhG/fEvpwr2LK+PjRCji1DdW21/+noytVPCU2iHKbe0/j21s/QAOUI6puE8ylh0CwshsdfEVmRchmAUN0Z4di3KnzVvWdEM5dpnfPV+JUaiDLv7+he0kVF5EcO53x5C0gKRdY7gljVDccf8+tJ9Xs4GlSwXsSmr+zkQ/81aCqkdA1e7Gi+Eifq6JDOl4/TeN6TmA1zScsxfVenv+6HFv/L1F7wzYSlZcVPbWPFta/V3HUeWB0QzggHCv3kFgV0vL3E6AiJv9HUCBztMsWBSpk++yvpGdQIQSYSjhIYoRJFyB++5AEuFOCSNIF3UfCwk2/W0xoo/FpwYAXh3LxtQve2AlUgQhP0IhXxjYPbg+Naay7nxqhlbIOST7qV3empSFhJ9qa434wneZBsCeJaOcqR2fAaJVa5mWcE+KJ5CkauGk9mkFKajpodz2NgL5otIfZg5Fqy9fDvRZFKC7SxI4a20ZtCjBXWcf2WG6oY8iYPKMbAqJ0HWDjWiYGWpg7HTC0HTETDDTXZJYHinOQj3zyDfkaRzTpIsagdAlceme2wkED1jaHO9ZFTV7K81IuTVhhJ381RxEhgNu8pcUwYUBtU8qhR8yz7e1GCw2/Z60m3mCQlil8vEjsyu4zg3tmCcIEaaZAqu6UUVC6MHFbUit5BAYUolIq23xrcdSAkOL6B5CPoU1nuP82VsST0SJ2HYcd6ppxakLVI+oHo61khn2zMYqDTyhiCWxzRBpaBAUjRLjnq+RKF3hngoY4/L7h4t/AQ4mulTLTpY+kNt9HR/Y2w0uNx3+y+PvCuPA/P3iv4dQHWFf43uRsceBjJfGu6Nv36iSNIXRv4EFOa9WN5e9GGcs4m7orIp2sSYyKQQmxRV2LBqRQHRWcjk1AOdn1wgOZzMd8g+HKBcz0LNZdQEpf2G2gCppxSrQ/wHP92sBqirZwjqXhnGnp06RKZ9ihdfenvtAGIBc4NIw4U+OKOYUvEn721tCDQhuwDzX4N0jGtxFbL+qF7XruKQBZguk/vdiOMMwLw1DWGGy6nCP+W9Tz+1nu0W5Yd4IqoajCE3T2t8SpMkIOHYLzPOJaEbSXTk69QIEG6N+7KlJ2Vv29+dFaofpquMCdxi4H1KZ2DyK+k2B2CYiUlek6GIU3jELOKovxl0CfSQiRV8ehCya2Iv6bAYDQjBvmdO9120BP2ANbqHyMoGu2B1Io4v0j/E4dFI6wMynJ+jU7eRQr1lYYX0tWaNw0hRKU/PgiZFQ38=
```

The first line is the hash of the unencrypted ÐApp-bundle (`a8cede...f1c189`). The text following is the encrypted, base-64-encoded ÐApp-bundle.

We need to remember the hash for the third step. We'll use the encrypted bundle text in the next step.

### Uploading the bundle

Now you need to find somewhere to upload the bundle text. There are many services online that allow you to deposit a bunch of text and retrieve it, *in raw format*, later. In general these services are called [pastebin](https://www.google.de/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=wikipedia%20pastebin)s.

Don't worry about encrypted pastebins; we've already encrypted the data so there's no risk there. The only important thing is that you end up with a *raw* rendition of the bundle text and that the URL for this is no more than 31 characters long. Turns out one called `pastebin.com` works rather well as its RAW URLs, not including the unneeded `http://` are exactly 31 characters.

So having uploaded it, you'll have a URL. It'll look something like `pastebin.com/raw.php?i=PvVPffnj`; remember this - you'll need it for later.

### Registering the URL hint

In AlethZero or AlethFive, enter `URLHinting` in the browser address bar.

In the text box labelled `Hash`, enter the hash from step 1. In the text box labelled `URL`, enter the URL from step 2. Make sure your URL is no more than 31 characters long and that when browsed it gives a pure text rendition of the bundle data from step 1.

Click the Suggest Hint button.

### Registering the ÐApp name

In AlethZero or AlethFive, enter `FixedFeeReg` in the browser address bar.

Now enter the address of your ÐApp. If you're on the Frontier network then at present you can only register names of 9 or more characters; anything less will be ignored. Note: you can register only lower-case names.

Make sure the account it is coming from is correct and hit the `Reserve` button; this will cost you 5 ether. 

You should see a bunch more options come up. (If you don't, just edit the name back and forth until you do.) Find the input box named `Content` and paste the bundle hash from step 1 into it. This associates the ÐApp name with the bundle and completes the deployment.

Once those transactions are mined, anyone typing your name into their AlethZero or AlethFive address bar will see you ÐApp!