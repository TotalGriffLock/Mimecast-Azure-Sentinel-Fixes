# Mimecast-Azure-Sentinel-Fixes
Reliability fixes made to the Mimecast log agent for Azure Sentinel

# Disclaimer
I am not a python coder! I am an infuriated security engineer. I am sure this could be done better.

I have found using the Mimecast Azure Sentinel agent in production that it has a few shortcomings. This patch resolves the below issues:

1. Flush SIEM API logs to disk regularly when retrieving a large number of logs. Without this the Mimecast code will try and download all available logs and store them in 1 python dictionary in RAM. If you have gigs of logs waiting to be retrieved (say on a large customer account after a period of extended downtime) this means gigs of RAM are needed for the dictionary. The patch changes this to flush to disk every 25 messages.
2. Skip invalid ZIP/JSON data served by the API. Without this the Mimecast code will fall over upon encountering bad data and stop retrieving logs. This means the bad data is still there next time it checks in and it will stop again, and your log retrieval will fail until Mimecast remove the bad data from the API queue. Unfortunately Mimecast's support solution to this is delete the application definition in the API and recreate it. This patch changes the code to report in the log when it has encountered bad data and moves on to the next data in the queue.

## Installation
To apply the patch, assuming you have installed the Mimecast agent in the default location of /opt:

```
curl -Lo /tmp/MPE.Mimecast.Azure.Sentinel-1.0.7-reliability.patch https://raw.githubusercontent.com/TotalGriffLock/Mimecast-Azure-Sentinel-Fixes/main/MPE.Mimecast.Azure.Sentinel-1.0.7-reliability.patch 
cd /opt
patch -p0 < /tmp/MPE.Mimecast.Azure.Sentinel-1.0.7-reliability.patch
```

Mimecast, if you are reading this, please:
1. Put your code on Github so we can submit pull requests / fixes, as your support don't seem keen on them
2. Distribute your code as python is meant to be distributed, not with a half built venv in a zip file which only works on 1 non current version of Ubuntu

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)
