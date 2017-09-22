---
layout: post
category: jekyll
---

Just a small snipet on how to send data to [TuneIn's AirAPI](http://tunein.com/broadcasters/api/) from golang. This way you can update your metadata information directly from your API, if you wish.

{% highlight golang }
import (
    "fmt"
    "net/http"
	"net/url"
    "time"
)

func tuneinAPI(artist string, title string) {

	partnerid := os.Getenv("TUNEIN_PARTNER_ID")
	partnerkey := os.Getenv("TUNEIN_PARTNER_KEY")
	stationid := os.Getenv("TUNEIN_STATION_ID")
	if partnerid == "" || partnerkey == "" || stationid == "" {
		fmt.Println(time.Now(), "No tunein creds, skipping.")
		return
	}

	var URL *url.URL
	URL, err := url.Parse("http://air.radiotime.com/Playing.ashx?")
	if err != nil {
		fmt.Println(time.Now(), "Tunein URL unavailable")
		return
	}

	parameters := url.Values{}
	parameters.Add("partnerId", partnerid)
	parameters.Add("partnerKey", partnerkey)
	parameters.Add("id", stationid)
	parameters.Add("artist", artist)
	parameters.Add("title", title)
	URL.RawQuery = parameters.Encode()

	fmt.Printf("Encoded URL is %q\n", URL.String())
	res, err := http.Get(URL.String())
	if err != nil {
		fmt.Println(err)
	}
	if res.StatusCode == 200 {
		fmt.Println(time.Now(), "TuneIn: "+artist+" - "+title)
	} else {
		fmt.Println(time.Now(), "Tunein submission failed.")
	}
}
{% endhighlight %}

Make sure you export the `TUNEIN` environment variables. This code is currently used over at [XTRadio](https://xtradio.org)