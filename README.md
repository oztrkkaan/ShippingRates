# ShippingRates

[![Build status](https://ci.appveyor.com/api/projects/status/gqq8i6nw932bn01v?svg=true)](https://ci.appveyor.com/project/alexeybusygin/shippingrates/)
[![NuGet Version](https://img.shields.io/nuget/v/ShippingRates.svg?style=flat-square)](https://www.nuget.org/packages/ShippingRates)

.NET wrapper to UPS, FedEx, and USPS APIs. Use it to retrieve shipping rates from these carriers.

## How to Install

Available in the [NuGet Gallery](http://nuget.org/packages/ShippingRates):

```
PM> Install-Package ShippingRates
```

## How to Use

```CSharp
NameValueCollection appSettings = ConfigurationManager.AppSettings;

// You will need a license #, userid and password to utilize the UPS provider.
string upsLicenseNumber = appSettings["UPSLicenseNumber"];
string upsUserId = appSettings["UPSUserId"];
string upsPassword = appSettings["UPSPassword"];

// You will need an account # and meter # to utilize the FedEx provider.
string fedexKey = appSettings["FedExKey"];
string fedexPassword = appSettings["FedExPassword"];
string fedexAccountNumber = appSettings["FedExAccountNumber"];
string fedexMeterNumber = appSettings["FedExMeterNumber"];

// You will need a userId to use the USPS provider. Your account will also need access to the production servers.
string uspsUserId = appSettings["USPSUserId"];

// Setup package and destination/origin addresses
var packages = new List<Package>();
packages.Add(new Package(12, 12, 12, 35, 150));
packages.Add(new Package(4, 4, 6, 15, 250));

var origin = new Address("", "", "06405", "US");
var destination = new Address("", "", "20852", "US"); // US Address

// Create RateManager
var rateManager = new RateManager();

// Add desired shipping providers
rateManager.AddProvider(new UPSProvider(upsLicenseNumber, upsUserId, upsPassword));
rateManager.AddProvider(new FedExProvider(fedexKey, fedexPassword, fedexAccountNumber, fedexMeterNumber));
rateManager.AddProvider(new USPSProvider(uspsUserId));

// (Optional) Add RateAdjusters
rateManager.AddRateAdjuster(new PercentageRateAdjuster(.9M));

// Call GetRates()
Shipment shipment = await rateManager.GetRatesAsync(origin, destination, packages);

// Iterate through the rates returned
foreach (Rate rate in shipment.Rates)
{
	Console.WriteLine(rate);
}
```

ShippingRates supports requesting a single rate from UPS and USPS.
To do so, include the rate description as a parameter of the provider constructor.
```CSHARP
rateManager.AddProvider(new USPSProvider(uspsUserId, "Priority Mail"));
rateManager.AddProvider(new UPSProvider(upsLicenseNumber, upsUserId, upsPassword, "UPS Ground"));
````
A list of valid shipping methods can be found in the documentation links below.

See the sample app in this repository for a working example.

#### International Rates
USPS requires a separate API call for retrieving rates international services.

The call works the same but use the USPSInternationalProvider instead. Your current USPS credentials will work with this and will return the available services between the origin and destination addresses.


## 3rd Party Docs

Developer documentation is often hard to find. The links below are provided as reference.

* [FedEx](http://www.fedex.com/us/developer/)
* [USPS](https://www.usps.com/business/web-tools-apis/welcome.htm)
* [UPS](https://www.ups.com/upsdeveloperkit)

## Credits

Originally forked from [DotNetShipping](https://github.com/kylewest/DotNetShipping) by [@kylewest](https://github.com/kylewest).
