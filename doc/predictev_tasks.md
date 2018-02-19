# Create new accounts and allocate initial supply

    cd programs/cli-wallet
    ./cli_wallet --server-rpc-endpoint="ws://127.0.0.1:9876"

    Logging RPC to file: logs/rpc/rpc.log
    Starting a new wallet
    497067ms th_a       main.cpp:154                  main                 ] wdata.ws_server: ws://127.0.0.1:9876
    497068ms th_a       main.cpp:159                  main                 ] wdata.ws_user:  wdata.ws_password:
    497071ms th_a       websocket_api.cpp:88          on_message           ] message: {"id":1,"result":true}
    497072ms th_a       websocket_api.cpp:88          on_message           ] message: {"id":2,"result":0}
    497072ms th_a       websocket_api.cpp:88          on_message           ] message: {"id":3,"result":3}
    Please use the set_password method to initialize a new wallet before continuing
    new >>>

    set_password "qwerty"

    locked >>>

    unlock "qwerty"

    import_key 5JNHfZYKGaomSFvd4NUdQ9qMcEAC43kujbfjueTHpVapX1Kzq2n

    create_account "initminer" "firstaccount" "" true

    create_account "initminer" "secondaccount" "" true

    list_my_accounts

    firstaccount            0.000 TESTS             0.000000 VESTS        0.000 TBD
    secondaccount           0.000 TESTS             0.000000 VESTS        0.000 TBD
    -------------------------------------------------------------------------
    TOTAL                   0.000 TESTS             0.000000 VESTS        0.000 TBD



    transfer "initminer" "firstaccount" "125.000 TESTS" "" true

    firstaccount          125.000 TESTS             0.000000 VESTS        0.000 TBD
    secondaccount           0.000 TESTS             0.000000 VESTS        0.000 TBD
    -------------------------------------------------------------------------
    TOTAL                 125.000 TESTS             0.000000 VESTS        0.000 TBD


    transfer "initminer" "secondaccount" "125.000 TESTS" "" true

    firstaccount          125.000 TESTS             0.000000 VESTS        0.000 TBD
    secondaccount         125.000 TESTS             0.000000 VESTS        0.000 TBD
    -------------------------------------------------------------------------
    TOTAL                 250.000 TESTS             0.000000 VESTS        0.000 TBD
