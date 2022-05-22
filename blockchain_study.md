### 스터디 내용 정리 
### 출처: https://docs.soliditylang.org/en/v0.8.14/ 

###### main.py
```python
"""
Python wrapper for handling geth and other utils.


Commands
    - run: run geth node (--bootstrap, --normal)
    - deploy: compile and deploy to geth node
"""

# built-in libraries
import os
import sys
BIN_PATH = os.path.abspath(__file__)
LIB_PATH = os.path.abspath(BIN_PATH + "/../../lib")
sys.path.append(LIB_PATH)

# user defined libraries
from custom_help import MyArgumentParser
from constant import *
from exceptions import *
import common
import net
import account

def getCmdParser():
    """
    Set commands and options\n

    Arguments:
        - None.
    Returns:
        - Argparser (MyArgumentParser).
    """


    parser = MyArgumentParser(prog=PROJECT_NAME, add_help=False)
    parser.add_argument('--v', '--version', action='version', version='%(prog)s v0.0.0', help='%(prog)s version')
    parser.add_argument('--h', '--help', help='{} command help message'.format(PROJECT_NAME))
    subparsers = parser.add_subparsers(help='%(prog)s actions', dest='command', title="Commands")
    parserInit = subparsers.add_parser("init", help="Init {} for a bootstrap node or a regular node".format(PROJECT_NAME))
    parserShow = subparsers.add_parser("show", help="Show network nodes / contracts of {}".format(PROJECT_NAME))
    parserDeploy = subparsers.add_parser("deploy", help="Deploy smart contracts in {} network".format(PROJECT_NAME))
    parserRun = subparsers.add_parser("run", help="Run smart contracts from {} network".format(PROJECT_NAME))
    parserSend = subparsers.add_parser("send", help="Send token to target address in {} network".format(PROJECT_NAME))


    # [init] optional options
    optionals = parserInit.add_argument_group()
    optionals.add_argument('--bootstrap', action='store_true', \
        help='Init your node with a bootstrap mode. If bootstrap node exists, do not use this option', dest=STR_BOOTSTRAP)

    # [show] optional options
    mandatorys = parserShow.add_mutually_exclusive_group(required=True)
    mandatorys.add_argument('--node', action='store_true', help="Show nodes in {} network".format(PROJECT_NAME), dest=STR_NODE)
    mandatorys.add_argument('--contract', action='store_true', help="Show contracts in {} network".format(PROJECT_NAME), dest=STR_CONTRACT)
    mandatorys.add_argument('--balance', action='store_true', help="Show balance of ETH", dest=STR_BALANCE)
    mandatorys.add_argument('--tx', action='store_true', help="Show transactions in {} network".format(PROJECT_NAME), dest=STR_TX)
    mandatorys.add_argument('--block', action='store_true', help="Show blocks in {} network".format(PROJECT_NAME), dest=STR_BLOCK)


    optionals = parserShow.add_argument_group()
    optionals.add_argument('--address', help="Show balance of specific address", dest=STR_ADDRESS)
    optionals.add_argument('--txhash', help="Show info of specific tx hash", dest=STR_TXHASH)

    # [deploy] optional options
    optionals = parserDeploy.add_mutually_exclusive_group(required=True)
    optionals.add_argument('--dirs',  nargs="+", help="Directory containing target solidity files", dest=STR_DIRS)
    optionals.add_argument('--files', nargs="+", help="Target solidity files", dest="files")

    # [run] mandatory options
    mandatorys = parserRun.add_mutually_exclusive_group(required=True)
    mandatorys.add_argument('--mining', action='store_true', help="Run mining from {} network".format(PROJECT_NAME), dest=STR_MINING)
    mandatorys.add_argument('--contract', action='store_true', help="Run contracts in {} network".format(PROJECT_NAME), dest=STR_CONTRACT)
    #mandatorys.add_argument('-contract',  help="Contract address (See address from show command)", dest=STR_CONTRACT, required=True)
    #mandatorys.add_argument('-param',  help="Parameters for contract. Must follow contract guide", dest=STR_PARAM, required=True)


    optionals = parserRun.add_argument_group()
    optionals.add_argument('--txhash', help="Run contract with txhash", dest=STR_TXHASH)

    # [send] mandatory options
    mandatorys = parserSend.add_argument_group()
    mandatorys.add_argument('--to', help='Address to receive your token', dest=STR_TO, required=True)
    mandatorys.add_argument('--eth', help='Amount of eth to send', dest=STR_ETH, required=True)

    # [send] optional options
    optionals = parserSend.add_argument_group()
    optionals.add_argument('--gas', help="Gas, if not given default gas fee", dest=STR_GAS)

    return parser

def main():
    # setup parser
    parser = getCmdParser()
    args = vars(parser.parse_args()) # change namespace to dict

    # print help message
    if len(sys.argv) == 1 or sys.argv[1] == '-h' or sys.argv[1] in HELP_OPTIONS :
        parser.print_help()
    else:

        if args[STR_COMMAND] == CMD_TYPE_INIT:
            if args[STR_BOOTSTRAP]:
                common.doInit()

            else:
                common.doInit(DEFAULT_ENODE)
        elif args[STR_COMMAND] == CMD_TYPE_SHOW:
            client = net.getClient()
            userAddr = account.getAccount(client) 
            common.doShow(client, userAddr, args)
        elif args[STR_COMMAND] == CMD_TYPE_DEPLOY:
            client = net.getClient()
            if args[STR_FILES] is None:
                args[STR_FILES] = []
            if args[STR_DIRS] is None:
                args[STR_DIRS] = []

            common.doDeploy(client, args[STR_FILES], args[STR_DIRS])
        elif args[STR_COMMAND] == CMD_TYPE_RUN:
            client = net.getClient()
            if args[STR_MINING] is True:
                common.doRunMining(client)
            if args[STR_CONTRACT] is True:
                common.doRunContract(client, args[STR_TXHASH])
        elif args[STR_COMMAND] == CMD_TYPE_SEND:
            client = net.getClient()
            userAddr = account.getAccount(client) 
            common.doSend(client, userAddr, args[STR_TO], args[STR_ETH], args[STR_GAS])


if __name__ == "__main__":
    try:
        main()
    except FileNotFoundException as e:
        print ("File not found: {}".format(e))
    except MultipleValueFoundException as e:
        print ("Mutiple values are found: {}".format(e))
    except NoValueFoundException as e:
        print ("No value is found: {}".format(e))
    except NetworkException as e:
        print ("Network connection is failed: {}".format(e))
    except Exception as e:
        import traceback
        traceback.print_exc()
        print ("Unexpected error: ", str(e))
```
###### account.py
```python
# built-in libraries
import json

# user-defined libraries
import security
from constant import *

def getAccount(client):
    """
    Description:
        Select an address of account from a list of accounts

    Args:
        - client (Web3)
    
    Return:
        - account (string)
    """

    if len(client.eth.accounts) > 1:
        for idx, account in enumerate(client.eth.accounts):
            print ("    {}. {}".format(idx+1, account))
        num = int(input("Enter number: "))
        account = client.eth.accounts[num-1]
    else:
        account = client.eth.accounts[0]

    return account

def getAccountList():
    """
    Description:
        Get a list of account (address) from a user's local PC

    Args:
    
    Return:
        - accountList (list): a list of account (address) from a user's keystore directory
    """
    files = security.getKeyStoreFiles()

    if len(files) > 0:
        accountList = []
        for mFile in files:
            with open(mFile) as f:
                s = f.read()
            f.close()
            accountList.append(json.loads(s)[STR_ADDRESS])
        return accountList
    else:
        return []
    
```
###### custom_help.py
```python
import argparse

class MyArgumentParser(argparse.ArgumentParser):
    def format_help(self):
        border = "\n" + "="*70+ "\n"
        formatter = self._get_formatter()
        formatter.add_text(border)

        # usage
        formatter.add_usage(self.usage, self._actions, self._mutually_exclusive_groups)

        # description
        formatter.add_text(self.description)

        # positionals, optionals and user-defined groups
        for action_group in self._action_groups:
            formatter.start_section(action_group.title)
            formatter.add_text(action_group.description)
            formatter.add_arguments(action_group._group_actions)
            formatter.end_section()

        # epilog
        formatter.add_text(self.epilog)
        formatter.add_text(border)

        # determine help from format above
        return formatter.format_help()
```

###### exceptions.py
```python
class FileNotFoundException(Exception):
    pass

class MultipleValueFoundException(Exception):
    pass

class NoValueFoundException(Exception):
    pass

class NetworkException(Exception):
    pass
```

###### taco_path.py
```python
# built-in libraries
import os
import socket

ROOT_PATH = "basepath"
UID = os.environ["USER"] + "_" + socket.gethostname()

def getBaseKeyStorePath():
    #TODO: change it to exact data dir path
    return ROOT_PATH + "mdata/{}/keystore".format(UID)

def getGethPath():
    return ROOT_PATH + "bin/geth"

def getDataDir():
    dirPath = ROOT_PATH +  "mdata/{}".format(UID)
    os.makedirs(dirPath, exist_ok=True)
    print ("dir path ({}) has been created".format(dirPath))
    return dirPath

def getGenesisPath():
    return ROOT_PATH + "metadata/genesis.json"

def getLogFile(bootstrapEnode=None):
    dirPath = ROOT_PATH + "log/{}".format(UID)
    os.makedirs(dirPath, exist_ok=True)
    print ("dir path ({}) has been created".format(dirPath))

    if not bootstrapEnode:
        return ROOT_PATH + "log/{}/bootstrap.log".format(UID)
    else:
        return ROOT_PATH + "log/{}/regular.log".format(UID)
```
###### net.py
```python
# built-in libraries
import os
import socket 
from web3 import Web3

# user-defined libraries
from constant import *

def getClient():
    """
    Open websocket with regular nodes in a user's local PC

    Return
        - w3 (object): web3 object
        - url (string):  url for selected geth client
    """

    os.environ["SOLC_BINARY"] =  SOLIDITY_COMPILER_PATH 
    url = "ws://" + socket.gethostbyaddr(socket.gethostname())[2][0] + ":" + str(DEFAULT_PORT)

    print ("Get client for {}".format(url))
    return Web3(Web3.WebsocketProvider(url))


def isConnected(w3):
    """
    Check if this user is able to connect Geth client

    Return
        - True if connected else False
    """
    
    if w3.isConnected():
        print ("Success to conenct Geth client. This node contains a list of accounts: {}".format(w3.eth.accounts))
        return True
    else:
        print ("Failed to connect Geth client!")
        return False
```

###### sec.py
```python
import sys
import socket
import glob

# user-defined libraries
import taco_path
from exceptions import * 
from constant import *

def getKeyStoreFiles():
    """
    Description:
        Get a list of keystore files
    
    Args:

    Return:
        - files (list): a list of file paths
    """

    BASE_PATH = taco_path.getBaseKeyStorePath()
    
    files = glob.glob(BASE_PATH + "/*")
    return files


def _getKeyStoreFile():
    """
    Description:
        Get a key store file path 
    
    Args:
        - client (Web3)
        - phrase (string) : a string of phrase

    Return:
        - key (string): a string of a private key
    """

    # TODO: specify base path for keystore
    BASE_PATH = taco_path.getBaseKeyStorePath()

    files = glob.glob(BASE_PATH + "/*")
    if len(files) == 1:
        return files[0]
    elif len(files) > 1: 
        raise MultipleValueFoundException()
    else:
        raise NoValueFoundException()

def getPrivateKey(client, phrase):
    """
    Description:
        Get private key of a user
    
    Args:
        - client (Web3)
        - phrase (string) : a string of phrase

    Return:
        - key (string): a string of a private key
    """

    keystoreFilePath = _getKeyStoreFile()

    with open(keystoreFilePath) as keyFile:
        encryptedKey = keyFile.read()
        privateKey = client.eth.account.decrypt(encryptedKey, phrase)
    return privateKey
    
def _readPhrase():
    """
    Description:
        Read one phrase word from a user. Maybe mulitple words in the near future (TODO)
    
    Args:

    Return:
        - word (string): a string of phrase
    """

    word = input ("Enter phrase word (one word): ")
    return word

def unlockAccount (client, addr):
    """
    Description:
        Unlock account
    
    Args:
        client (Web3): Web3 object
        addr (string): Address

    Return:
        res (bool): True if success else False
    """

    return client.geth.personal.unlock_account(addr, _readPhrase(), duration=DEFAULT_UNLOCK_DURATION)

```

###### common.py
```python
# built-in libraries
import socket
import os
import sys
from termcolor import colored
import glob

# user defined libraries
import taco_path
from constant import *
from exceptions import *
import contract
import account
import security
import display
import simpleutils

def doInit(bootstrapEnode=None):
    """
    Description:
        Init and run GETH node

    Args:
        - bootstrapEnode (bool): Node if regular node else bootstrap enode

    Return:
        - None 
    """

    # Params: mandatory for geth command
    gethBin = taco_path.getGethPath()
    genesisPath = taco_path.getGenesisPath()
    dataDir = taco_path.getDataDir()
    logFile = taco_path.getLogFile(bootstrapEnode)
    ip = socket.gethostbyaddr(socket.gethostname())[2][0]
    port = DEFAULT_PORT

    # network id is used for identification in the same network ()
    # e.g. mainnet (1), dev (2018)
    # Reference 
    #   chain id has a different role compared to network id,
    #   but mostly has the same value with network id
    networkID = DEFAULT_NETWORK_ID 

   # print a user's input
    print ()
    print ("="*BORDER_LENGTH)
    print ("- genesis : {}".format(genesisPath))
    print ("- datadir : {}".format(dataDir))
    print ("- ip      : {}".format(ip))
    print ("- port    : {}".format(port))
    print ("- net id  : {}".format(networkID))
    print ("- log file: {}".format(logFile))
    print ("="*BORDER_LENGTH)
    print ()
    ans = input("Type 'y' or 'yes' if the settings are ok: ")

    if ans.lower() == 'y' or ans.lower() == 'yes':

        cmd = "{} --datadir {} init {}".format(gethBin, dataDir, genesisPath)
        print ("Run cmd: {}".format(cmd))

        stdout, stderr = simpleutils.runCmd(cmd)
        stdout = str(stdout.encode('euc-kr', 'ignore'))
        stderr = str(stderr.encode('euc-kr', 'ignore'))
        
        # stderr and stdout has no difference in here
        # rather, the result message is important
        if len(stderr) > 0:
            if "Successfully wrote" in stderr:
                print (colored(stderr, "green"))
            if "Maximum peer count" in stderr:
                print (colored(stderr, "green"))
            else:
                print (colored(stderr, "red"))
                return
        if len(stdout) > 0:
            print (colored(stdout, "green"))
        # Account: account is necessary for mining
        # if any account doesn't exist, create a new account
        accountList = account.getAccountList()

        if len(accountList) == 0:
            print ("No account is found. Create an account")
            cmd = "/user/socat/user/sangwoo.noh/other_project/blk/bin/geth account new --datadir {}".format(dataDir)
            os.system(cmd)
        else:
            print()
            for idx, accnt in enumerate(accountList):
                print ("{}. 0x{}".format(idx+1, accnt))
                
            print ()
        # Run commmand: run and exit is ok (don't need to use simpleutils)
        # run
        if bootstrapEnode is None:
            cmd = "{} --datadir {} --mine --ws --nat extip:{} --networkid {} --ws.api web3,eth,debug,personal,net,miner --ws.addr 0.0.0.0 --ws.port {} --ws.origins='*' --http.vhosts '*' --http.corsdomain '*'  --allow-insecure-unlock" \
                .format(gethBin, dataDir, ip, networkID, port)
        else:
            cmd = "{} --datadir {} --mine --ws --nat extip:{} --networkid {} --ws.api web3,eth,debug,personal,net,miner --ws.addr 0.0.0.0 --ws.port {} --ws.origins='*' --http.corsdomain '*'   --bootnodes {} --allow-insecure-unlock"\
                .format(gethBin, dataDir, ip, networkID, port, bootstrapEnode)

        print ("Run cmd: {}".format(cmd))
        #stdout, stderr = simpleutils.runCmd(cmd)
        print ("Press to (Ctrl+z, bg) to run process background...")
        os.system(cmd)
    else:
        print ("canceled by user")

def doShow(client, userAddr, args):
    """
    Description:
        Show balance / transactions / blocks

    Args:
        - client (Web3): web3 object
        - userAddr (string): user's address
        - args (dict): given options by command

    Return:
        - None 
    """

    coinbase = client.eth.coinbase

    print ("Your account: ", coinbase)

    if args[STR_BLOCK]: 
        display.showBlock(client)
    if args[STR_TX]: 
        display.showTx(client, args[STR_TXHASH])
 
    if args[STR_BALANCE]: 
        address = args[STR_ADDRESS] if args[STR_ADDRESS] is not None else userAddr
        display.showBalance(client, address)

def doDeploy(client, files=[], dirs=[]):
    """
    Description:
        Compile and deploy solditiy files
    
    Args:
        - client (Web3): web3 object
        - files (list): a list of files
        - dirs (list) a list of dirs (TODO)
    """

    targetFileList = []

    # handle files
    for f in files:
        f = os.path.abspath(f)
        print ("file: ", f)
        targetFileList.append(f)

    # handle dirs 
    # TODO:
    for d in dirs:
        targetFileList += glob.glob(d + "/*")
    
   # compile files
    print ("target file list: ", targetFileList)

    bytecode, abi = contract.compile(targetFileList)
    print ("bytecode: ", bytecode)
    print ("abi     : ", abi)

    # deploy with compiled result
    txReceipt = contract.deploy(client, {STR_BIN: bytecode, STR_ABI: abi})
    print ("tx receipt : ", txReceipt)

def doRunMining(client):
    """
    Descripton:
        Start mining

    Args:
        - client (Web3): Web3 Object
    
    Returns:
        None
    """

    client.geth.miner.start(DEFAULT_MINER_CNT)
def doRunContract(client, txHash):
    txReceipt = client.eth.wait_for_transaction_receipt(txHash)
    address = txReceipt.contractAddress
    ###
    # TODO: important! ABI must be different (no hard coding.) --> abi must be stored in somewhere to make it different for each smart contrac0wt 
    ###
    abi =  [{'constant': False, 'inputs': [{'name': '_greeting', 'type': 'string'}], 'name': 'setGreeting', 'outputs': [], 'payable': False, 'stateMutability': 'nonpayable', 'type': 'function'}, {'constant': True, 'inputs': [], 'name': 'greet', 'outputs': [{'name': '', 'type': 'string'}], 'payable': False, 'stateMutability': 'view', 'type': 'function'}, {'constant': True, 'inputs': [], 'name': 'greeting', 'outputs': [{'name': '', 'type': 'string'}], 'payable': False, 'stateMutability': 'view', 'type': 'function'}, {'inputs': [], 'payable': False, 'stateMutability': 'nonpayable', 'type': 'constructor'}]

    contract.run(client, {STR_ADDRESS: address, STR_ABI: abi})

def doSend(client, sender, receiver, amount):
    """
    Descripton:
        Send token from sender to receiver

    Args:
        - client (Web3): Web3 Object
        - sender (string): An address of sender
        - receiver (string): An address of receiver
        - amount (float): Amount of Ether
    
    Returns:
        None
    """

    security.unlockAccount(client, sender)

    params = {
        "to": receiver, 
        "from": sender,
        "value": client.toWei(amount, 'ether')
    }
    tx = client.eth.send_transaction(params)
    print ("tx (result): ", tx.hex())

```

###### display.py
```python

from constant import * 
def showTx(client, txHash=None):
    """
    Description:
        Show transactions from each block

    Args:
        - client (Web3): web3 object

    Returns:
        None
    """

    print ("="*BORDER_LENGTH)
    print ("%-70s    |    %-50s    ->    %-50s    %-10s"%("Tx Hash", "Sender", "Receiver", "Eth"))
    print ("="*BORDER_LENGTH)
   if txHash is None:
        blkNo = client.eth.block_number
    
        print ()
        
        cnt = 0
        for i in range(blkNo, 1, -1):
            blk = client.eth.getBlock(i)
            for tx in blk["transactions"]:
                txInfo = client.eth.getTransaction(tx.hex())
                print ("tx info: ", txInfo)
                print ("%-70s    |    %-50s    ->    %-50s    %-10s"%(txInfo["hash"].hex(), txInfo["from"], txInfo["to"], client.fromWei(txInfo["value"], "ether")))
                print ("="*BORDER_LENGTH)
                cnt += 1
        print ()
        print ("Total transactions: ", cnt)
        print ()
    else:
        txInfo = client.eth.getTransaction(txHash)
        print ("%-50s    ->    %-50s    %-10s"%(txInfo["from"], txInfo["to"], client.fromWei(txInfo["value"], "ether")))
        print ("="*BORDER_LENGTH)

        print (txInfo)

def showBalance(client, address):
    """
    Description:
        Show balances

    Args:
        - client (Web3): web3 object
        - address (string): address to check balance

    Returns:
        None
    """

    ethBalance = client.fromWei(client.eth.get_balance(address), 'ether')
    print ()
    print ("="*BORDER_LENGTH)
    print ("{} has {} eth!".format(address, round(ethBalance, 2)))
    print ("="*BORDER_LENGTH)

def showBlock(client):
    """
    Description:
        Show blocks

    Args:
        - client (Web3): web3 object

    Returns:
        None
    """

    blkNo = client.eth.block_number

    print ()
    print ("="*BORDER_LENGTH)
    print ("Block information summary")
    print ("="*BORDER_LENGTH)

    cnt = 0
    for i in range(blkNo, 1, -1):
        blk = client.eth.getBlock(i)
        if len(blk["transactions"]) == 0:
            #print ("skip empty block")
            pass
        else:
            from datetime import datetime
            print ("Block miner                : {}".format(blk["miner"]))
            print ("Block creation time        : {}".format(datetime.fromtimestamp(blk["timestamp"])))
            print ("Number of Tx in this block : {}".format(len(blk["transactions"])))
            for idx, tx in enumerate(blk["transactions"]):
                print ("    {}. {}".format(idx+1, tx.hex()))
            print ("="*BORDER_LENGTH)
            cnt += 1

    print ()
    print ("Total valid blocks : ", cnt)
    print () 
    


```

###### compile.pyppp
```python
# built-in libraries
import os
import sys
import solcx
import socket
from web3 import Web3
import json

from pprint import pprint

def _unlockAccount (client, addr):
    """
    Description:
        Unlock account
    
    Args:
        client (Web3): Web3 object
        addr (string): Address

    Return:
        res (bool): True if success else False
    """

    return client.geth.personal.unlock_account(addr, input ("Enter phrase word (one word): "), duration=1000)

def _deploy(abi, bytecode):
    """
    Description:
        Deploy a smart cData

    Args:
        - account (Eth account)
        - cData (dict): contract dict with ABI and ADDRESS keys

    Return:
        - txReceipt (transaction result)
    """


    URL = "ws://pivy1207:12345"
    client = Web3(Web3.WebsocketProvider(URL))
    mContract = client.eth.contract(bytecode=bytecode, abi=abi)

    # TODO: security
    client.eth.default_account = client.eth.coinbase
    _unlockAccount(client, client.eth.default_account)
    txHash = mContract.constructor().transact({"gas": 2100000}) # gas is required!! # too low gas doesn't work...

    
    txReceipt = client.eth.wait_for_transaction_receipt(txHash, timeout=10000)

    print ("Contract has been deployed: txhash ({})".format(txHash))
    print ("Tx receipt has been received: {}".format(txReceipt))

    if txReceipt is not None and txReceipt["status"]:
        print ("Success to deploy a contract!")
        return txReceipt
    else:
        print ("Failed to deploy a contract!")
        return None
def _compile(targetFileList: list):
    """
    Description:
        Compile a smart contract

    Args:
        - targetFileList (list): a file to compile
    
    Return
        - bytecode (string)
        - abi (string)
    """

    print ("Try compiling contract : {}!".format(targetFileList))

    # change into string to list if string is given

    # compile
    os.environ["SOLC_BINARY"] = "/user/socat/user/sangwoo.noh/other_project/blk/bin/solc_0.8"
    res = solcx.compile_files(targetFileList, solc_binary=os.environ["SOLC_BINARY"],  output_values=["abi", "bin"], base_path = "/user/socat/user/sangwoo.noh/other_project/blk/")

    unlinked_bytecode = ""
    finalCode = ""
    for key in res.keys():
        if "/mytoken.sol" in key:
            print ("key: ", key)
            abi = res[key]["abi"]
            bytecode = res[key]["bin"]
            print ("-"*150)
            break

    pprint (bytecode)
    pprint (abi)

    if bytecode is not None and abi is not None:
        print ("Success to compile a smart contract: {}".format(targetFileList))
        return (bytecode, abi)
    else:
        print ("Failed to compile a smart contract: {}".format(targetFileList))
        return None

def main():
    filePath = os.path.abspath(sys.argv[1])
    print ("given file path: ", filePath)

    bytecode, abi = _compile([filePath])
    jsonRes = json.dumps(abi)
    with open ("./abi", 'w') as wf:
        wf.write(jsonRes)
    wf.close()

    assert(bytecode is not None)
    print ("="*100)
    print ("byte code: ", bytecode)
    print ("="*100)
    _deploy(abi, bytecode)
    

if __name__ == "__main__":
    main()


```

###### run.py
```python
# built-in libraries
import os
import sys
from web3 import Web3
import json

from pprint import pprint

TX_HASH = "0x46f8f5262a5b99650d7333c71ee86cc386d7d3975108261aaee0a566ec7bcf48"

def _unlockAccount (client, addr):
    """
    Description:
        Unlock account
    
    Args:
        client (Web3): Web3 object
        addr (string): Address

    Return:
        res (bool): True if success else False
    """

    return client.geth.personal.unlock_account(addr, input ("Enter phrase word (one word): "), duration=1000)
def main():
    URL = "ws://pivy1207:12345"
    client = Web3(Web3.WebsocketProvider(URL))
    
    tx_receipt = client.eth.wait_for_transaction_receipt(TX_HASH)
    print ("tx receipt: ", tx_receipt)

    with open("./abi") as f:
        abi = json.load(f)

    pprint (abi)

    print ("contract address: ", tx_receipt.contractAddress)
    #receiver = Web3.toChecksumAddress("0x5c216a7697c395802ef065d2973783910fd18cad")
    greeter = client.eth.contract(address=Web3.toChecksumAddress(tx_receipt.contractAddress), abi=abi)
    print ("account: ", client.eth.coinbase)

    client.eth.default_account = client.eth.coinbase
    _unlockAccount(client, client.eth.default_account)

    #greeter.functions.setGreeting("bye").transact()
    #tx_receipt = client.eth.wait_for_transaction_receipt(TX_HASH)
    print("result: ", greeter.functions.greet().call())


    #mToken = client.eth.contract(address=tx_receipt.contractAddress, abi=abi)
    #print (mToken.functions.transfer(receiver, 100).call())
    #print (mToken.functions.totalSupply().call())

    #from web3.contract import ConciseContract
    #reader = ConciseContract(client.eth.contract(address=tx_receipt.contractAddress, abi=abi))
    #print("name : ", reader.name())
    #print("symbol : ", reader.symbol())
    #print("decimals : ", str(reader.decimals()))
    #print("totalSupply : ", str(reader.totalSupply()))
    #print("owner : ", reader.owner())

if __name__ == "__main__":
    main()


```
