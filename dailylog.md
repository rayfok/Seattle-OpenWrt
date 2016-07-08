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
            - connectionserversendblocks.py
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
                - Expected stop time: 1 second
                - Measured stop time: 1.55 seconds 
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

- Trying to fix problem with ut_nm_subprocess.py hanging
    - Gets stuck after "Writing vessl dictionary"
    - Successfully runs nminit.py and nmmain.py
    - Gets stuck on line 17 (try: sys.std.in.read())
    - Can't find file v2/nodemanager.old






