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



