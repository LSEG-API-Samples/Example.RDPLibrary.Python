# Refinitiv Data Platform Library for Python

## Summary

The following series of examples demonstrate how to programmatically access content residing within the **Refinitiv Data Platform** using a single, ease of use library called the **Refinitiv Data Platform Library for Python**. 

This library provides a set of ease-of-use functions and classes that gives your applications a uniform access to the breath and depth of financial data and services available on the Refinitiv Data Platform. The same Python code can be used to retrieve data whatever the access point your application uses to connect to the Refinitiv Data Platform (either via a direct connection or via Eikon or via the Refinitiv Workspace or even via a deployed Enterprise Platform).

## 3 layers for all coders

The library provides several abstraction layers (Function, Content and Delivery) enabling different programming styles and technics suitable for all developers from Financial Coders to Seasoned Developers (synchronous function calls, async/await, event driven). Based on these layers, the examples defined within this section have been organized as follows:

### **Function**

The *Function* examples target simple functions that every financial coder can use to easily retrieve financial items like Price, News, Historical Data, etc. The *Function* layer is generally used for use cases that do not require advanced programming technics.

### **Content**

The *Content* examples target higher-level abstractions representing financial items like Price, News, Historical Data, etc. The *Content* layer can easily be used by both professional developers and financial coders. It provides great flexibility for commonly used financial objects.

### **Delivery**

The *Delivery* examples target the interfaces defined within the lowest abstraction layer of the library.  The examples will use different delivery modes such as Streaming, Data, Alert and Bulk services.


# Some examples

## Import the Refinitiv Data Platform Library

```python
import refinitiv.dataplatform as rdp
```

## Chose your platform access point...

### ...either directly to the Refinitiv Data Platform...

```python
rdp.open_platform_session(
    '8e9bxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1b035d', 
    rdp.GrantPassword(
        'my_login', 
        'my_password'
    )
)
```

### ...or via Eikon/Refinitiv Workspace...

```python
rdp.open_desktop_session('8e9bxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1b035d')
```

### ...or via a Deployed Platform

```python
rdp.open_deployed_platform_session(
    '8e9bxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1b035d',
    'my_deployed_platform_ip',
    'my_dacs_user_name'
)
```

## Real-time Pricing - Get Snapshots

```python
rdp.get_snapshot(
    universe = ['GBP=','JPY='], 
    fields   = ['BID','ASK']
)

```

|   | Instruments | BID | ASK |
| - | ----------- | --- | --- |
| 0 | GBP= | 1.3211 | 1.3212 |
| 1 | JPY= | 108.6100 | 108.6200 |

## Real-time Pricing - Get Streaming Data

```python
streaming_prices = rdp.StreamingPrices(
    instruments = ['EUR=', 'GBP=', 'JPY=', 'CAD='], 
    fields = ['DSPLY_NAME', 'BID', 'ASK'],
    on_update = lambda streaming_price, instrument_name, fields : 
        print("Update received for {}: {}".format(instrument_name, fields))
)

streaming_prices.open()
```

Output:

    <StreamState.Open: 3>

    Update received for JPY=: {'DSPLY_NAME': 'BARCLAYS     LON', 'BID': 109.59, 'ASK': 109.62}
    Update received for GBP=: {'DSPLY_NAME': 'ASANPACIFIBK MOW', 'BID': 1.341, 'ASK': 1.3411}
    Update received for EUR=: {'DSPLY_NAME': 'UBS          ZUR', 'BID': 1.117, 'ASK': 1.1174}
    Update received for CAD=: {'DSPLY_NAME': 'HSBC         LON', 'BID': 1.3165, 'ASK': 1.3167}
    Update received for JPY=: {'DSPLY_NAME': 'ASANPACIFIBK MOW', 'BID': 109.59, 'ASK': 109.61}
    Update received for GBP=: {'DSPLY_NAME': 'INTERPROMBAN MOW', 'BID': 1.341, 'ASK': 1.3412}
    Update received for EUR=: {'DSPLY_NAME': 'RBS          LON', 'BID': 1.117, 'ASK': 1.1174}
    Update received for CAD=: {'DSPLY_NAME': 'CIBC         TOR', 'BID': 1.316, 'ASK': 1.3164}
    Update received for JPY=: {'DSPLY_NAME': 'BARCLAYS     LON', 'BID': 109.59, 'ASK': 109.62}
    Update received for GBP=: {'DSPLY_NAME': 'INTERPROMBAN MOW', 'BID': 1.341, 'ASK': 1.3413}
    Update received for EUR=: {'DSPLY_NAME': 'BARCLAYS     LON', 'BID': 1.117, 'ASK': 1.1174}
    Update received for CAD=: {'DSPLY_NAME': 'CIBC         TOR', 'BID': 1.316, 'ASK': 1.3164}
    Update received for JPY=: {'DSPLY_NAME': 'ASANPACIFIBK MOW', 'BID': 109.59, 'ASK': 109.61}
    Update received for GBP=: {'DSPLY_NAME': 'BARCLAYS     LON', 'BID': 1.341, 'ASK': 1.3414}

## Historical Pricing - Get Interday Summaries

```python
rdp.get_historical_price_summaries(
    universe = 'VOD.L', 
    interval = rdp.Intervals.DAILY,
    fields = ['BID','ASK','OPEN_PRC','HIGH_1','LOW_1','TRDPRC_1','NUM_MOVES','TRNOVR_UNS']
)
```

|   | BID | ASK | OPEN_PRC | HIGH_1 | LOW_1 | TRDPRC_1 | NUM_MOVES | TRNOVR_UNS |
| - | --- | --- | -------- | ------ | ----- | -------- | --------- | ---------- |
| 2019-12-12 | 144.32 | 144.34 | 144.42 | 145.66 | 143.46 | 144.18 | 12631.0 | 8498347218.71154 |
| 2019-12-11 | 143.58 | 143.6 | 142.72 | 144.8 | 142.62 | 143.58 | 10395.0 | 8815450412.65353 |
| 2019-12-10 | 142.74 | 142.78 | 143.84 | 143.84 | 141.48 | 142.74 | 10311.0 | 8070285210.45742 |
| ... | ... | ... | ... | ... | ... | ... | ... | ... |
| 2019-11-18 | 152.1 | 152.12 | 154.74 | 155.66 | 152.0 | 152.12 | 14606.0 | 19322988639.34 |
| 2019-11-15 | 154.6 | 154.62 | 160.68 | 160.68 | 154.06 | 154.6326 | 17035.0 | 31993013818.37456 |

## News - Get Headlines

```python
rdp.get_news_headlines(query = 'LFR', count = 350)
```

|   | versionCreated | text | storyId | sourceCode |
| - | -------------- | ---- | ------- | ---------- |
| 2019-12-12 06:51:36.518 | 2019-12-12 06:51:36.518 | SGX FTSE China A50 (Z9) intraday : prudence. | urn:newsml:reuters.com:20191212:nGUR92Pj4t:1 | NS:GURU |
| 2019-12-12 06:49:52.011 | 2019-12-12 06:49:52.011 | SSE Composite intraday : biais haussier au-des... | urn:newsml:reuters.com:20191212:nGUR8gt362:1 | NS:GURU |
| 2019-12-12 06:49:33.973 | 2019-12-12 06:49:33.973 | Election du Conseil fédéral: "repenser la form... | urn:newsml:reuters.com:20191212:nNRAafovmk:1 | NS:SDASDE |
| ... | ... | ... | ... | ... |
| 2019-12-11 23:27:13.673 | 2019-12-11 23:27:13.673 | Le nouveau système en huit questions | urn:newsml:reuters.com:20191211:nNRAafll6g:1 | NS:LESECH |
| 2019-12-11 23:27:13.653 | 2019-12-11 23:27:13.653 | Les clefs du royaume | urn:newsml:reuters.com:20191211:nNRAaflped:1 | NS:LESECH |

350 rows × 4 columns

# Learning materials

 To learn more about the Refinitiv Data Platform Library for python just connect to the Refinitiv Developer Community. By [registering](https://developers.refinitiv.com/en/register) and [login](https://developers.refinitiv.com/content/devportal/en_us/initCookie.html) to the Refinitiv Developer Community portal you will get free access to a number of learning materials like [Quick Start guides](https://developers.refinitiv.com/en/api-catalog/refinitiv-data-platform/refinitiv-data-platform-libraries/quick-start), [Tutorials](https://developers.refinitiv.com/en/api-catalog/refinitiv-data-platform/refinitiv-data-platform-libraries/tutorials), [Documentation](https://developers.refinitiv.com/en/api-catalog/refinitiv-data-platform/refinitiv-data-platform-libraries/documentation) and much more.  

# Help and Support

If you have any questions regarding the API usage, please post them on the [Refinitiv Data Platform Q&A Forum](https://community.developers.refinitiv.com/spaces/321/index.html). The Refinitiv Developer Community will be very pleased to help you. 


