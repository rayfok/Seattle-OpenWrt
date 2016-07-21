# Seattle on OpenWrt

### 7/7/16

##### Running unit tests for OpenWrt 

- repy_v2:
    - Specified repy program is unsafe
        - In `safe.py`, increased timeout for OpenWrt routers to 100 seconds ([ref](https://github.com/rf1591/repy_v2/blob/master/safe.py#L121))
        - Remaining failed tests:
            - stoptimesaresane.py
			- removefileperformsresourceaccounting.py
			- randombytesisresourcelimited.py
			- openfileperformsresourceaccounting.py
			- openfileconsumesfilehandles.py
			- nannyupdatesresourceconsumption.py
			- listfilesperformsresourceaccounting.py
			- listenforconn-resources.py
			- listenclosesend.py
			- initialusevaluesaresane.py
			- getresourcedataisvalid.py
			- filewriteatperformsresourceaccounting.py
			- filereadatperformsresourceaccounting.py
			- fileclosereleasesresource.py
			- filecloseconcurrecy.py 
			- createthreadissane.py
			- connectionserversendblocks.py

    - Bad GETTID call (previously addressed in [Issue #98](https://github.com/SeattleTestbed/repy_v2/issues/98) in repy_v2)
        - Changing `thread_id` to `pid` in [linux_api.py](https://github.com/rf1591/repy_v2/blob/master/linux_api.py#L224)  seems to quick-fix the unfound file error, but raises others...
        - Remaining failed tests:
            - connectionsendwilleventuallyblock.py
                - exceptions.AssertionError
            - connectionserversendblocks.py
                - exceptions.AssertionError
            - filecloseconcurrency.py 
                - exeception_hierarchy.FileInUseError: Cannot open file "test.write.junk.data" because it is already open
            - getthreadnamebasictest.py
                - Timed out 
            - listenclosesend.py
                - exceptions.AssertionError (line 48)
            - openfileconsumesfilehandles.py
                - exeception_hierarchy.FileInUseError: Cannot open file "trying.to.create.this.file" because it is already open
            - openfilesanitycheckargs.py
                - Timed out 
            -  sendmessagebasictest.py
                - Timed out
            - stoptimesaresane.py
            	- length of stoptimesarray > 2
- seattlelib_v2 
    - Changed timeout threshold to 100 seconds
    - Changed `thread_id` to `pid` to avoid GETTID syscall error
    - Remaining failed tests:
        - dylinkrepyportabilityimportmodulessymbols.py
            - Unexpected contents in variable: local override
        - httpretrieve_*.py
            - dy_import_module_symbols is not defined
- nodemanager
    - Stuck on writing vessel dictionary (>30 minutes)
    - NMClientException from nmclient_createhandle

- librepy
    - recvmess-basic.r2py
        - exceptions.AssertionError
    - socket-nonblocksend.r2py
        - socket closed by the remote end
    - socket-sockpeername.r2py
        - socket has been closed
    - socket-timeoutsend.r2py
        - socket closed by the remote end
    - socket-timeoutrecv.r2py 
        - socket closed by the remote end
    - waitforconn-basic.r2py
        - can't bind to ip
    - waitforconn-reusethreadpool.r2py
        - can't bind to ip

### 7/8/16

- Trying to fix problem with `ut_nm_subprocess.py` hanging
    - Gets stuck after "Writing vessel dictionary"
    - Successfully runs `nminit.py` and `nmmain.py`
    - Gets stuck on line 17 (try: sys.std.in.read())
    - Can't find file v2/nodemanager.old

### 7/11/16

- Continuing to debug issues in nodemanager unit tests for OpenWrt
   - utf.py is not sending a kill subprocess message to be received by stdin
   - Attempted changing wait time for subprocess 
       - Changed from 30 to 120 seconds in[utf.py](https://github.com/SeattleTestbed/utf/blob/master/utf.py#L342)
   - Tests finally begin running after ~35 minutes
   - Encountered errors in nmclient.r2py in nmclient_createhandle() 

### 7/12/16

- It turns out despite nmclient_createhandle() causing tests to fail, the unit test framework can be succesfully run on OpenWrt. It takes ~10-15 minutes for a single test however, and has yet to complete a full test cycle of the entire module.
- Successes
    - runs nminit 
    - stops subprocess `ut_nm_subprocess.py` 
	- obtains pid 

### 7/13/16

- Added MIPS 32-bit syscall to `linux_api.py`

  ```
  if platform.machine() == 'mips':
    GETTID = 4222
  ```

- nmclient_createhandle() in line 298, raising NMClientException `IOError(2, 'No such file or directory')`

### 7/18/16

- Unit tests (and any git calls) require installation of the `git-http` package
- In node_manager unit tests:
    - RepyArgumentError("Provided destip is not valid! IP: ...")
    - Tests fail after ~30 minutes
    - Probably due to router losing connection with NAT forwarder

### 7/19/16

- Reviewing Albert's [commit](https://github.com/SeattleTestbed/utf/pull/73/commits) for updates to utf.py addressing issues #69,71,72
	- Ran the unit tests with updated utf.py successfully:
		- repy_v2 
	```
	root@OpenWrt:/tmp/repy_v2/RUNNABLE# python utf.py -t -a
Testing module: repyv2api
    	Running: ut_repyv2api_commandlineargs.py                	[ PASS ] [ 21.60s ]
    	Running: ut_repyv2api_connectionduplicateclose.py       	[ PASS ] [ 122.5s ]
    	Running: ut_repyv2api_connectionrecvlocalclose.py       	[ PASS ] [ 118.6s ]
    	Running: ut_repyv2api_connectionrecvremoteclose.py      	[ PASS ] [ 118.5s ]
    	Running: ut_repyv2api_connectionrecvwouldblock.py       	[ PASS ] [ 117.2s ]
    	Running: ut_repyv2api_connectionsendlocalclose.py       	[ FAIL ] [ 72.74s ]
--------------------------------------------------------------------------------
Standard error :
..............................Produced..............................
Monitor process died! Terminating!
/usr/bin/python: can't open file '/tmp/repy_v2/RUNNABLE/safe_check.py': [Errno 2] No such file or directory

..............................Expected..............................
None
--------------------------------------------------------------------------------
    	Running: ut_repyv2api_connectionsendremoteclose.py      	[ PASS ] [ 105.9s ]
    	Running: ut_repyv2api_connectionsendwilleventuallyblock.py  [ FAIL ] [ 115.7s ]
--------------------------------------------------------------------------------
Standard error :
..............................Produced..............................
---
Uncaught exception!
---
Following is a full traceback, and a user traceback.
The user traceback excludes non-user modules. The most recent call is displayed last.

Full debugging traceback:
  "repy.py", line 154, in execute_namespace_until_completion
  "/tmp/repy_v2new/RUNNABLE/virtual_namespace.py", line 117, in evaluate
  "/tmp/repy_v2new/RUNNABLE/safe.py", line 590, in safe_run
  "ut_repyv2api_connectionsendwilleventuallyblock.py", line 42, in <module>

User traceback:
  "ut_repyv2api_connectionsendwilleventuallyblock.py", line 42, in <module>

Exception (with type 'exceptions.AssertionError'):
---

..............................Expected..............................
None
--------------------------------------------------------------------------------
    	Running: ut_repyv2api_connectionsendwillnotblock.py     	[ PASS ] [ 98.24s ]
    	Running: ut_repyv2api_connectionserversendblocks.py     	[ FAIL ] [ 114.5s ]
--------------------------------------------------------------------------------
Standard error :
..............................Produced..............................
---
Uncaught exception!
---
Following is a full traceback, and a user traceback.
The user traceback excludes non-user modules. The most recent call is displayed last.

Full debugging traceback:
  "repy.py", line 154, in execute_namespace_until_completion
  "/tmp/repy_v2new/RUNNABLE/virtual_namespace.py", line 117, in evaluate
  "/tmp/repy_v2new/RUNNABLE/safe.py", line 590, in safe_run
  "ut_repyv2api_connectionserversendblocks.py", line 42, in <module>

User traceback:
  "ut_repyv2api_connectionserversendblocks.py", line 42, in <module>

Exception (with type 'exceptions.AssertionError'):
---

..............................Expected..............................
None
--------------------------------------------------------------------------------
    	Running: ut_repyv2api_createlockblocks.py               	[ PASS ] [ 121.7s ]
    	Running: ut_repyv2api_createlocknonblocking.py          	[ PASS ] [ 100.3s ]
    	Running: ut_repyv2api_createthreadhasresourcecontrols.py	[ PASS ] [ 103.4s ]
    	Running: ut_repyv2api_createthreadissane.py             	[ PASS ] [ 101.4s ]
    	Running: ut_repyv2api_exitallstopsanotherthread.py      	[ PASS ] [ 99.67s ]
    	Running: ut_repyv2api_exitallstopscurrentthread.py      	[ PASS ] [ 99.08s ]
    	Running: ut_repyv2api_filecloseconcurrecy.py            	[ FAIL ] [ 99.24s ]
--------------------------------------------------------------------------------
Standard error :
..............................Produced..............................
---
Uncaught exception!
---
Following is a full traceback, and a user traceback.
The user traceback excludes non-user modules. The most recent call is displayed last.

Full debugging traceback:
  "repy.py", line 154, in execute_namespace_until_completion
  "/tmp/repy_v2new/RUNNABLE/virtual_namespace.py", line 117, in evaluate
  "/tmp/repy_v2new/RUNNABLE/safe.py", line 590, in safe_run
  "ut_repyv2api_filecloseconcurrecy.py", line 34, in <module>
  "/tmp/repy_v2new/RUNNABLE/namespace.py", line 1207, in wrapped_function
  "/tmp/repy_v2new/RUNNABLE/emulfile.py", line 179, in emulated_open
  "/tmp/repy_v2new/RUNNABLE/emulfile.py", line 271, in __init__

User traceback:
  "ut_repyv2api_filecloseconcurrecy.py", line 34, in <module>

Exception (with class 'exception_hierarchy.FileInUseError'): Cannot open file "test.write.junk.data" because it is already open!
---

..............................Expected..............................
None
--------------------------------------------------------------------------------
Standard out :
..............................Produced..............................
Read worked after close!

..............................Expected..............................
None
--------------------------------------------------------------------------------
    	Running: ut_repyv2api_fileclosemakesothercallsfail.py   	[ PASS ] [ 100.7s ]
    	Running: ut_repyv2api_fileclosereleasesresource.py      	[ PASS ] [ 100.1s ]
    	Running: ut_repyv2api_filereadatbasictest.py            	[ PASS ] [ 100.6s ]
    	Running: ut_repyv2api_filereadatpastendoffile.py        	[ PASS ] [ 103.4s ]
    	Running: ut_repyv2api_filereadatperformsresourceaccounting.py [ PASS ] [ 100.6s ]
    	Running: ut_repyv2api_filereadatsanitychecksargs.py     	[ PASS ] [ 100.1s ]
    	Running: ut_repyv2api_filewriteatbasictest.py           	[ PASS ] [ 101.8s ]
    	Running: ut_repyv2api_filewriteatperformsresourceaccounting.py [ PASS ] [ 102.5s ]
    	Running: ut_repyv2api_filewriteatsanitychecksargs.py    	[ PASS ] [ 105.7s ]
    	Running: ut_repyv2api_getlasterrorworks.py              	[ PASS ] [ 100.7s ]
    	Running: ut_repyv2api_getresourcedataisvalid.py         	[ PASS ] [ 100.9s ]
    	Running: ut_repyv2api_getthreadnamebasictest.py         	[ PASS ] [ 104.6s ]
    	Running: ut_repyv2api_initialusevaluesaresane.py        	[ PASS ] [ 92.45s ]
    	Running: ut_repyv2api_listencloselisten.py              	[ PASS ] [ 102.3s ]
    	Running: ut_repyv2api_listencloselisten2.py             	[ PASS ] [ 99.17s ]
    	Running: ut_repyv2api_listenclosesend.py                	[ FAIL ] [ 100.5s ]
--------------------------------------------------------------------------------
Standard error :
..............................Produced..............................
---
Uncaught exception!
---
Following is a full traceback, and a user traceback.
The user traceback excludes non-user modules. The most recent call is displayed last.

Full debugging traceback:
  "repy.py", line 154, in execute_namespace_until_completion
  "/tmp/repy_v2new/RUNNABLE/virtual_namespace.py", line 117, in evaluate
  "/tmp/repy_v2new/RUNNABLE/safe.py", line 590, in safe_run
  "ut_repyv2api_listenclosesend.py", line 48, in <module>

User traceback:
  "ut_repyv2api_listenclosesend.py", line 48, in <module>

Exception (with type 'exceptions.AssertionError'):
---

..............................Expected..............................
None
--------------------------------------------------------------------------------
    	Running: ut_repyv2api_listenforconn-addrbinding.py      	[ PASS ] [ 102.1s ]
    	Running: ut_repyv2api_listenforconn-args.py             	[ PASS ] [ 99.68s ]
    	Running: ut_repyv2api_listenforconn-badport.py          	[ PASS ] [ 99.90s ]
    	Running: ut_repyv2api_listenforconn-cleanup.py          	[ PASS ] [ 100.1s ]
    	Running: ut_repyv2api_listenforconn-dup.py              	[ PASS ] [ 98.82s ]
    	Running: ut_repyv2api_listenforconn-resources.py        	[ PASS ] [ 99.21s ]
    	Running: ut_repyv2api_listfilesbasictest.py             	[ PASS ] [ 101.0s ]
    	Running: ut_repyv2api_listfilesperformsresourceaccounting.py [ PASS ] [ 98.63s ]
    	Running: ut_repyv2api_multipleopenconnections.py        	[ PASS ] [ 100.3s ]
    	Running: ut_repyv2api_multipleopenconnections2.py       	[ PASS ] [ 101.0s ]
    	Running: ut_repyv2api_nannyupdatesresourceconsumption.py	[ PASS ] [ 99.62s ]
    	Running: ut_repyv2api_openconnectionalreadyalistening.py	[ PASS ] [ 102.3s ]
    	Running: ut_repyv2api_openconnectionduplicatetuple.py   	[ PASS ] [ 99.07s ]
    	Running: ut_repyv2api_openconnectionfailswithoutlisten.py   [ PASS ] [ 100.3s ]
    	Running: ut_repyv2api_openconnectionresourceforbidden.py	[ PASS ] [ 101.4s ]
    	Running: ut_repyv2api_openconnectionsanitychecksargs.py 	[ PASS ] [ 102.5s ]
    	Running: ut_repyv2api_openconnectiontimeout.py          	[ PASS ] [ 103.7s ]
    	Running: ut_repyv2api_openconnectionwithbadlocalip.py   	[ PASS ] [ 99.66s ]
    	Running: ut_repyv2api_openfileconsumesfilehandles.py    	[ FAIL ] [ 97.85s ]
--------------------------------------------------------------------------------
Standard error :
..............................Produced..............................
---
Uncaught exception!
---
Following is a full traceback, and a user traceback.
The user traceback excludes non-user modules. The most recent call is displayed last.

Full debugging traceback:
  "repy.py", line 154, in execute_namespace_until_completion
  "/tmp/repy_v2new/RUNNABLE/virtual_namespace.py", line 117, in evaluate
  "/tmp/repy_v2new/RUNNABLE/safe.py", line 590, in safe_run
  "ut_repyv2api_openfileconsumesfilehandles.py", line 23, in <module>
  "/tmp/repy_v2new/RUNNABLE/namespace.py", line 1207, in wrapped_function
  "/tmp/repy_v2new/RUNNABLE/emulfile.py", line 179, in emulated_open
  "/tmp/repy_v2new/RUNNABLE/emulfile.py", line 271, in __init__

User traceback:
  "ut_repyv2api_openfileconsumesfilehandles.py", line 23, in <module>

Exception (with class 'exception_hierarchy.FileInUseError'): Cannot open file "trying.to.create.this.file" because it is already open!
---

..............................Expected..............................
None
--------------------------------------------------------------------------------
    	Running: ut_repyv2api_openfileduplicatefilefails.py     	[ PASS ] [ 99.34s ]
    	Running: ut_repyv2api_openfileperformsresourceaccounting.py [ PASS ] [ 98.25s ]
    	Running: ut_repyv2api_openfilesanitychecksargs.py       	[ PASS ] [ 102.1s ]
    	Running: ut_repyv2api_randombytesisresourcelimited.py   	[ PASS ] [ 101.2s ]
    	Running: ut_repyv2api_randombytesissane.py              	[ PASS ] [ 103.5s ]
    	Running: ut_repyv2api_removefileissane.py               	[ PASS ] [ 102.9s ]
    	Running: ut_repyv2api_removefileperformsresourceaccounting.py [ PASS ] [ 101.9s ]
    	Running: ut_repyv2api_sendmessage5ktest.py              	[ PASS ] [ 98.69s ]
    	Running: ut_repyv2api_sendmessagebasictest.py           	[ PASS ] [ 99.57s ]
    	Running: ut_repyv2api_sendmessagecheckstypes.py         	[ PASS ] [ 101.3s ]
    	Running: ut_repyv2api_sleepbasictest.py                 	[ PASS ] [ 113.1s ]
    	Running: ut_repyv2api_stoptimesaresane.py               	[ FAIL ] [ 137.1s ]
--------------------------------------------------------------------------------
Standard out :
..............................Produced..............................
Initial length of stoptimes array is more than 2! Array: [(5.6275210380554199, 0.43421397209166914), (7.1123778820037842, 0.19427709579467845), (7.4576890468597412, 0.093007993698115849), (8.5657799243927002, 0.1272230625152595), (9.7055299282073975, 0.11358709335325657), (10.845445871353149, 0.1408171176910429), (11.995501041412354, 0.12218089103699015), (13.177633047103882, 0.16893105506897044), (14.416598081588745, 0.15713696479797435), (15.646430969238281, 0.091216039657595623), (16.815619945526123, 0.24446310997009134), (18.135740995407104, 0.16194205284118724), (19.297612905502319, 0.15782399177551556), (20.537996053695679, 0.049617004394534093), (21.597618103027344, 0.02780780792235049), (22.626087951663065, 0.14999999999709257), (23.833268880844116, 0.062070941925053799), (24.899436950683594, 0.12733287811279581), (26.088994922640267, 0.11000000000349531), (27.186893882753795, 0.18316097259521767), (27.529004755022472, 0.094845962524409795), (28.522244873049206, 0.12925405502319621), (29.678898859026957, 0.14565200805664349), (30.788943815234232, 0.16777510643005654), (31.807871866229105, 0.21881399154663587), (32.98978190422349, 0.18877191543577608), (34.126755762103134, 0.10100922584534189), (35.206717777255112, 0.16449303627014446), (36.333507823947006, 0.08423600196838876), (37.474949884417588, 0.13515205383301067), (37.766710805895859, 0.088390064239497685), (38.721173810961777, 0.25241212844848704), (39.942186880114609, 0.16254706382751727), (41.057791757586533, 0.089952087402330959), (42.064673948290879, 0.11199483871460459), (43.137667942050034, 0.1436768531799365), (44.237788963320786, 0.18995184898376748), (45.372411775591904, 0.18880410194397256), (46.496735858920151, 0.1099489688873163), (47.577898788455063, 0.16227002143860149), (48.73196296692186, 0.13920683860779095), (49.816744852068955, 0.12990007400513193), (50.897799777987534, 0.18993301391601846), (52.040054845812851, 0.18012113571167275), (53.277833986285263, 0.1966958522796638), (54.406773853304911, 0.10994205474852236), (55.387906837466288, 0.25235600471496865), (56.561924982073832, 0.22584481239319131), (57.777930784228373, 0.069892024993901455), (58.816699790957499, 0.14795417785644817), (59.955637979510355, 0.24792890548706126), (61.212383794787455, 0.25233297348022532), (62.41673593521409, 0.081041908264147365), (63.496949958804178, 0.13323302268982218), (64.586740779879618, 0.07995901107788583), (65.672276782992411, 0.13795514106750774), (66.74775080681138, 0.20000000000000283), (67.928864784247708, 0.21341390609741494), (69.108869857795072, 0.16341300010681438)]
We expect to be stopped at least for 0.8 seconds! Were stopped for: 0.0935439586639

..............................Expected..............................
None
--------------------------------------------------------------------------------
    	Running: ut_repyv2api_tcpserver_wouldblock.py           	[ PASS ] [ 131.8s ]
    	Running: ut_repyv2api_typetypeisblocked.py              	[ PASS ] [ 130.6s ]
    	Running: ut_repyv2api_unicodedisallowed.py              	[ PASS ] [ 130.4s ]
    	Running: ut_repyv2api_virtualnamespace-eval.py          	[ PASS ] [ 270.0s ]
    	Running: ut_repyv2api_virtualnamespace-init.py          	[ PASS ] [ 266.2s ]
    	Running: ut_repyv2api_virtualnamespacecontextsafety.py  	[ PASS ] [ 190.6s ]
Total time taken to run tests on module repyv2api is: 7636.2
	```
	- nodemanager
	    - same errors in nmclient_createhandle() as before



