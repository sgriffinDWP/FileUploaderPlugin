## FileUploader Plugin for Xamarin iOS, Android, UWP, Mac, tvOS and watchOS
Simple cross platform plugin for file multipart uploads.

<p align="center">
<img src="https://github.com/CrossGeeks/FileUploaderPlugin/blob/master/FileUploader%20Plugin%20-%20Android.gif" title="Android"/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<img src="https://github.com/CrossGeeks/FileUploaderPlugin/blob/master/FileUploader%20Plugin%20-%20iOS.gif" title="iOS"/>
</p>

## Features

- Multipart file uploading with headers and parameters
- Upload multiple files at once
- Upload request progress feedback
- Upload files by using bytes or file path
- Set custom boundary

### Setup
* Available on NuGet: http://www.nuget.org/packages/Plugin.FileUploader [![NuGet](https://img.shields.io/nuget/v/Plugin.FileUploader.svg?label=NuGet)](https://www.nuget.org/packages/Plugin.FileUploader/)
* Install into your PCL project and Client projects.


**Platform Support**

|Platform|Version|
| ------------------- | :------------------: |
|Xamarin.iOS|iOS 7+|
|Xamarin.Android|API 15+|
|Windows 10 UWP|10+|
|Xamarin.Mac|10.9+|
|watchOS|2.0+|
|tvOS|9.0+|

### API Usage

Call **CrossFileUploader.Current** from any project or PCL to gain access to APIs.

You can upload a file using the file path or bytes.


**FilePathItem**
```csharp
/// <summary>
/// Path: File path location.
/// FieldName: Request field name for the file to be uploaded
/// </summary>
public class FilePathItem
{
    public string Path { get; } 
    public string FieldName {get; } 
}
```


**FileBytesItem**
```csharp
/// <summary>
/// FieldName: Request field name for the file to be uploaded
/// Bytes: File bytes.
/// Name: Name of the file.
/// </summary>
public class FileBytesItem
{
    public string Name { get; }
    public string FieldName { get; }
    public byte[] Bytes { get; }
}
```


**UploadFileAsync**
```csharp
        /// <summary>
        /// Upload file using file path
        /// </summary>
        /// <param name="url">Url for file uploading</param>
        /// <param name="fileItem">File path item to be uploaded</param>
        /// <param name="headers">Request headers</param>
        /// <param name="parameters">Additional parameters for upload request</param>
        /// <param name="boundary">Custom part boundary</param>
        /// <returns>FileUploadResponse</returns>
        Task<FileUploadResponse> UploadFileAsync(string url, FilePathItem fileItem, IDictionary<string,string> headers =null,IDictionary < string, string> parameters = null, string boundary = null);

        /// <summary>
        /// Upload files using file path
        /// </summary>
        /// <param name="url">Url for file uploading</param>
        /// <param name="fileItems">File path items to be uploaded</param>
        /// <param name="tag">Tag reference of the upload request</param>
        /// <param name="headers">Request headers</param>
        /// <param name="parameters">Additional parameters for upload request</param>
        /// <param name="boundary">Custom part boundary</param>
        /// <returns>FileUploadResponse</returns>
        Task<FileUploadResponse> UploadFileAsync(string url, FilePathItem[] fileItems,string tag, IDictionary<string, string> headers = null, IDictionary<string, string> parameters = null, string boundary = null);

        /// <summary>
        /// Upload file using file bytes
        /// </summary>
        /// <param name="url">Url for file uploading</param>
        /// <param name="fileItem">File bytes item to be uploaded</param>
        /// <param name="headers">Request headers</param>
        /// <param name="parameters">Additional parameters for upload request</param>
        /// <param name="boundary">Custom part boundary</param>
        /// <returns>FileUploadResponse</returns>
        Task<FileUploadResponse> UploadFileAsync(string url, FileBytesItem fileItem, IDictionary<string, string> headers = null, IDictionary<string, string> parameters = null, string boundary = null);


        /// <summary>
        /// Upload files using file bytes
        /// </summary>
        /// <param name="url">Url for file uploading</param>
        /// <param name="fileItems">File bytes of items to be uploaded</param>
        /// <param name="tag">Tag reference of upload request</param>
        /// <param name="headers">Request headers</param>
        /// <param name="parameters">Additional parameters for upload request</param>
        /// <param name="boundary">Custom part boundary</param>
        /// <returns>FileUploadResponse</returns>
        Task<FileUploadResponse> UploadFileAsync(string url, FileBytesItem[] fileItems,string tag, IDictionary<string, string> headers = null, IDictionary<string, string> parameters = null,string boundary = null);
```

Usage sample:

Uploading from a file path
```csharp

    CrossFileUploader.Current.UploadFileAsync("<URL HERE>", new FilePathItem("<REQUEST FIELD NAME HERE>","<FILE PATH HERE>"), new Dictionary<string, string>()
                {
                   {"<HEADER KEY HERE>" , "<HEADER VALUE HERE>"}
                }
    );

```

Uploading from a file bytes
```csharp

  CrossFileUploader.Current.UploadFileAsync("<URL HERE>", new FileBytesItem("<REQUEST FIELD NAME HERE>","<FILE BYTES HERE>","<FILE NAME HERE>"), new Dictionary<string, string>()
                {
                   {"<HEADER KEY HERE>" , "<HEADER VALUE HERE>"}
                }
  );

```
Uploading multiple files at once
```csharp

 CrossFileUploader.Current.UploadFileAsync("<URL HERE>", new FilePathItem[]{
    new FilePathItem("file",path1),
	new FilePathItem("file",path2),
	new FilePathItem("file",path3)
 },"Upload Tag 1");

 ```
#### Events in FileUploader
When any file upload completed/failed you can register for an event to fire:
```csharp
/// <summary>
/// Event handler when file is upload completes succesfully
/// </summary>
event EventHandler<FileUploadResponse> FileUploadCompleted; 
```

```csharp
/// <summary>
/// Event handler when file is upload fails
/// </summary>
event EventHandler<FileUploadResponse> FileUploadError; 
```

```csharp
 /// <summary>
 /// Event handler when file upload is in progress, indicates what's the upload progress so far
 /// </summary>
 event EventHandler<FileUploadProgress> FileUploadProgress;
 ```

For events **FileUploadCompleted** and **FileUploadError** you will get a FileUploadResponse with the status and response message:

```csharp
public class FileUploadResponse
{
        public string Tag { get; }
        public string Message { get; }
        public int StatusCode { get; }
        public IReadOnlyDictionary<string, string> Headers { get; }
}
```

Usage sample:
```csharp

  CrossFileUploader.Current.FileUploadCompleted += (sender, response) =>
  {
    System.Diagnostics.Debug.WriteLine($"{response.StatusCode} - {response.Message}");
  };
  
  CrossFileUploader.Current.UploadFileAsync($"<UPLOAD URL HERE>",new FileItem("<FIELD NAME HERE>","<FILE PATH HERE>"));

```

While upload is in progress you can get feedback on event **FileUploadProgress**

You will get a FileUploadProgress with the total bytes sent, total request byte length and progress percentage

```csharp
public class FileUploadProgress
{
        public long TotalBytesSent { get; }
        public long TotalLength { get; }
        public double Percentage { get; }
	public string Tag { get; }

}
```

Usage sample:
```csharp
  CrossFileUploader.Current.FileUploadProgress += (sender, uploadProgress) =>
  {
      System.Diagnostics.Debug.WriteLine($"{uploadProgress.Tag} - {uploadProgress.TotalBytesSent} - {uploadProgress.Percentage}");
  };
```
### **IMPORTANT**

### iOS:
On AppDelegate.cs

```csharp
    /**
     * Save the completion-handler we get when the app opens from the background.
     * This method informs iOS that the app has finished all internal processing and can sleep again.
     */
    public override void HandleEventsForBackgroundUrl(UIApplication application, string sessionIdentifier, Action completionHandler)
    {
        FileUploadManager.UrlSessionCompletion = completionHandler;
    }
```

Also consider on iOS 9+, your URL must be secured or you have to add the domain to the list of exceptions. See https://github.com/codepath/ios_guides/wiki/App-Transport-Security

### Android:

There are some android specific static properties to specify timeout information:

```csharp
        public static TimeUnit UploadTimeoutUnit { get; set; } = TimeUnit.Minutes;
        public static long SocketUploadTimeout { get; set; } = 5;
        public static long ConnectUploadTimeout { get; set; } = 5;
``` 

Above you can see the default values. But you can change the value for the timeouts and unit by setting it from your Android project(Could be on MainActivity) like this:

```csharp
        FileUploadManager.UploadTimeoutUnit = TimeUnit.Minutes;
        FileUploadManager.SocketUploadTimeout = 10;
        FileUploadManager.ConnectUploadTimeout  = 5;
``` 


#### Contributors

* [Rendy Del Rosario](https://github.com/rdelrosario)
* [Charlin Agramonte](https://github.com/char0394)
* [Angel Andres Mañon](https://github.com/AngelAndresM)
