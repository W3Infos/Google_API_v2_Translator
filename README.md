# Google_API_v2_Translator
Translate Web content using google tranlate api + javascript
#Google Translate V2 cannot hanlde large text translations from C#

Note: You can also use POST to invoke the API if you want to send more data in a single request. The q parameter in the POST body must be less than 5K characters. To use POST, 
you must use the X-HTTP-Method-Override header to tell the Translate API to treat the request as a GET (use X-HTTP-Method-Override: GET).


var request = WebRequest.Create (uri);
request.Method = "POST";
request.ContentType = "application/x-www-form-urlencoded";
request.Headers.Add("X-HTTP-Method-Override", "GET");

var body = new StringBuilder();
body.Append("key=SECRET");
body.AppendFormat("&source={0}", HttpUtility.UrlEncode(source));
body.AppendFormat("&target={0}", HttpUtility.UrlEncode(target));
 //--
body.AppendFormat("&q={0}", HttpUtility.UrlEncode(text));

var bytes = Encoding.ASCII.GetBytes(body.ToString());
if (bytes.Length > 5120) throw new ArgumentOutOfRangeException("text");

request.ContentLength = bytes.Length;
using (var output = request.GetRequestStream())
{
    output.Write(bytes, 0, bytes.Length);
}
-----------------------------------------------------------------------
using (var webClient = new WebClient())
{
    webClient.Headers.Add("X-HTTP-Method-Override", "GET");
    var data = new NameValueCollection() 
    { 
        { "key", GoogleTranslateApiKey }, 
        { "source", "en" }, 
        { "target", "fr"}, 
        { "q", "<p>Hello World</p>" } 
    };
    try
    {
        var responseBytes = webClient.UploadValues(GoogleTranslateApiUrl, "POST", data);
        var json = Encoding.UTF8.GetString(responseBytes);
        var result = JsonConvert.DeserializeObject<dynamic>(json);
        var translation = result.data.translations[0].translatedText;
    }
    catch (Exception ex)
    {
        loggingService.Error(ex.Message);
    }
} 
