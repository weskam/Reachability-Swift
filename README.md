# Swift - Reachability

Updated for: Swift 3, Xcode Beta 6

```swift
import Foundation
import SystemConfiguration

public class Reachability {

    class func isConnectedToNetwork() -> Bool {

        var zeroAddress = sockaddr_in()
        zeroAddress.sin_len = UInt8(MemoryLayout<sockaddr_in>.size)
        zeroAddress.sin_family = sa_family_t(AF_INET)

        guard let defaultRouteReachability = withUnsafePointer(to: &zeroAddress, {
            $0.withMemoryRebound(to: sockaddr.self, capacity: 1) {
                SCNetworkReachabilityCreateWithAddress(nil, $0)
            }
        }) else {
            return false
        }

        var flags: SCNetworkReachabilityFlags = []
        if !SCNetworkReachabilityGetFlags(defaultRouteReachability, &flags) {
            return false
        }

        let isReachable = flags.contains(.reachable)
        let needsConnection = flags.contains(.connectionRequired)

        return (isReachable && !needsConnection)
    }
}


//Call in project
if Reachability.isConnectedToNetwork() == true {
    print("CONNECTED")
} else {
    print("CONNECTION FAILED")
}

```


How to get it done using ALAMOFIRE


```swift
import SystemConfiguration
import Alamofire

func networkListener() {
        let net = NetworkReachabilityManager()
        net?.startListening()

        net?.listener = {status in

            if  net?.isReachable ?? false {

                if ((net?.isReachableOnEthernetOrWiFi) != nil) {
                    //Do something here...
                } else if(net?.isReachableOnWWAN)! {
                    //Do something here...
                }

            } else {
                let alertController = UIAlertController(title: "No Internet Connection", message: "Make sure your device is connected to the internet.", preferredStyle: .alert)

                let defaultAction = UIAlertAction(title: "Ok", style: .default, handler: nil)
                alertController.addAction(defaultAction)

                self.present(alertController, animated: true, completion: nil)
            }
        }
    }
```
