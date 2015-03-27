# ASP.NET Web API 2

## 服务端数据定义 

### HttpRestult 

	/// <summary>
    ///     定义 HTTP 处理结果，不含业务处理数据
    ///     @ 2010-2015 Matteo Swift
    /// </summary>
    /// <remarks>
    ///     版本：4.15.3.2
    ///     日期：2015年3月2日
    ///     @ 2010-2015 Matteo Swift
    /// </remarks>
    public class HttpResult
    {
        /// <summary>
        ///     获取服务端响应请求时长，单位毫秒
        /// </summary>
        public long ExecMsec { get; internal set; }

        /// <summary>
        ///     获取服务端响应过程是否有错误
        /// </summary>
        public bool HasError { get; internal set; }

        /// <summary>
        ///     获取服务端响应过程中的信息描述
        /// </summary>
        /// <returns>
        ///     仅返回服务端响应信息，不返回业务处理相关信息
        /// </returns>
        public string Message { get; internal set; }
    }

    /// <summary>
    ///     定义 HTTP 处理结果，泛化业务结果数据，通常结果数据为复杂数据类型
    ///     @ 2010-2015 Matteo Swift
    /// </summary>
    /// <remarks>
    ///     版本：4.15.3.2
    ///     日期：2015年3月2日
    ///     @ 2010-2015 Matteo Swift
    /// </remarks>
    public class HttpResult<T> : HttpResult
    {
        /// <summary>
        ///     获取业务处理数据
        /// </summary>
        /// <returns>
        ///     当 <see cref="T:HasError" /> 为 True 时，此属性无论是否为 null 业务数据均无意义，否则必须实例化 <see cref="T:T" /> 类型数据
        /// </returns>
        public T Value { get; internal set; }
    }

## 服务端示例代码

**由于涉及加密向量IV，因此未提供 SecurityService 的代码实现!**

1. 定义 SecurityModel ，用于存储明文、密文、向量等信息。
2. 定义 ApiController ，通过 Get 和 Post 方式公开 API 接口。

### SecurityModel & SecurityController 

	/// <summary>
    ///     定义数据安全模型
	///     @ 2010-2015 Matteo Swift
    /// </summary>
    public class SecurityModel
    {
        /// <summary>
        ///     明文
        /// </summary>
        public string PlainText { get; set; }

        /// <summary>
        ///     密文
        /// </summary>
        public string CipherText { get; set; }

        /// <summary>
        ///     向量
        /// </summary>
        public byte[] IV { get; set; }
    }

	/// <summary>
    ///     数据加密/解密服务
	///     @ 2010-2015 Matteo Swift
    /// </summary>
    public class SecurityController : ApiController
    {
        /// <summary>
        ///     数据加密
        /// </summary>
        /// <param name="plain">明文</param>
        /// <returns>返回数据安全模型实例</returns>
        public HttpResult<SecurityModel> Get(string plain)
        {
            var watch = Stopwatch.StartNew();
            var result = new HttpResult<SecurityModel> { Value = new SecurityModel { PlainText = plain } };
            try
            {
                byte[] iv = null;
                //result.Value.CipherText = SecurityService.Encrypt(result.Value.PlainText, ref iv);
                result.Value.IV = iv;
            }
            catch (Exception ex)
            {
                result.HasError = true;
                result.Message = ex.GetDetails();
            }
            watch.Stop();
            result.ExecMsec = watch.ElapsedMilliseconds;
            return result;
        }
  
        /// <summary>
        ///    数据加密或解密数据
        /// </summary>
        /// <param name="data">数据安全模型实例</param>
        /// <returns>数据安全模型实例</returns>
        public HttpResult<SecurityModel> Post([FromBody] SecurityModel data)
        {
            var watch = Stopwatch.StartNew();
            var result = new HttpResult<SecurityModel> { Value = data };
            try
            {
                var iv = data.IV ?? null;
                //如果明文不为空则进行加密，否则进行解密
                if (!string.IsNullOrEmpty(data.PlainText))
                {
                   	//data.CipherText = SecurityService.Encrypt(data.PlainText, ref iv);
                }
                else
                {   
					//此处当密文为空时会引发异常
                   	//data.PlainText = SecurityService.Decrypt(data.CipherText, ref iv);
                }
                data.IV = iv;
            }
            catch (Exception ex)
            {
                result.HasError = true;
                result.Message = ex.GetDetails();
            }
            watch.Stop();
            result.ExecMsec = watch.ElapsedMilliseconds;
            return result;
        }
    }


## 终端数据定义

### HttpResult 

	/// <summary>
    ///     定义 HTTP 处理结果，不含业务处理数据
    ///     @ 2010-2015 Matteo Swift
    /// </summary>
    /// <remarks>
    ///     版本：4.15.3.2
    ///     日期：2015年3月2日
    /// </remarks>
    public class HttpResult
    {
        /// <summary>
        ///     初始化HTTP处理结果实例
        /// </summary>
        public HttpResult()
        {
            this.ExecMsec = -1;
        }

        /// <summary>
        ///     获取或设置服务端响应信息
        /// </summary>
        public string Message { get; set; }

        /// <summary>
        ///     获取或设置服务端响应是否有错误
        /// </summary>
        public bool HasError { get; set; }

        /// <summary>
        ///     获取或设置服务端响应毫秒数
        /// </summary>
        /// <returns>
        ///     当为返回 -1 时，通常是服务端响应过程出现异常或请求地址错误
        /// </returns>
        public int ExecMsec { get; set; }
    }
  
    /// <summary>
    ///     定义 HTTP 处理结果，泛化业务处理数据
    ///     @ 2010-2015 Matteo Swift
    /// </summary>
    /// <typeparam name="T">通常为 MVVM 中的 Model 类或集合</typeparam>
    /// <remarks>
    ///     版本：4.15.3.2
    ///     日期：2015年3月2日
    /// </remarks>
    public class HttpResult<T> : HttpResult
    {
        /// <summary>
        ///     获取业务处理数据
        /// </summary>
        public T Value { get; set; }
    }

### Callback 

	/// <summary>
    ///     定义 HTTP 请求回调结果
    ///     @ 2010-2015 Matteo Swift
    /// </summary>
    /// <remarks>
    ///     版本：4.15.3.2
    ///     日期：2015年3月2日
    /// </remarks>
    public class Callback
    {
        internal Callback()
        {
            this.Data = new HttpResult();
        }

        /// <summary>
        ///     获取用户定义状态对象，通常作用于回调函数中的数据处理
        /// </summary>
        [IgnoreDataMember]
        public object UserState { get; internal set; }

        /// <summary>
        ///     获取HTTP请求过程出现的异常错误
        /// </summary>
        [IgnoreDataMember]
        public Exception Error { get; internal set; }

        /// <summary>
        ///     获取一个布尔值，以表明是否 HTTP 请求有异常错误
        /// </summary>
        [IgnoreDataMember]
        public bool HasError
        {
            get { return this.Error != null; }
        }

        /// <summary>
        ///     获取 HTTP 原始资源地址
        /// </summary>
        [IgnoreDataMember]
        public Uri RequestUri { get; internal set; }

        /// <summary>
        ///     获取 HTTP 请求方式，支持GET、POST、PUT、DELETE
        /// </summary>
        [IgnoreDataMember] 
		public string Method { get; internal set; }

        /// <summary>
        ///     获取服务端响应结果，JSON 格式数据反序列化数据实例，限定 <see cref="HttpResult" /> 的类型实例
        /// </summary>
        public HttpResult Data { get; internal set; }
    }

    /// <summary>
    ///     定义 HTTP 请求回调结果
    ///     @ 2010-2015 Matteo Swift
    /// </summary>
    /// <remarks>
    ///     版本：4.15.3.2
    ///     日期：2015年3月2日
    /// </remarks>
    public class Callback<T> : Callback
    {
        /// <summary>
        ///     默认初始化<see cref="T:T" />类型实例
        /// </summary>
        internal Callback()
        {
            //通过反射，略有性能消耗
            this.Data = Activator.CreateInstance<T>();
        }

        /// <summary>
        ///     获取服务端响应结果， JSON 格式数据反序列化数据实例，通常为 <see cref="HttpResult" /> 的泛型实例，亦可是其它数据类型
        /// </summary>
        public new T Data { get; internal set; }
    }


## 数据序列化与反序列化
###	JsonSerializer
    /// <summary>
    ///     定义 JSON 数据序列化与反序列化辅助类
    ///     @ 2010-2015 Matteo Swift
    /// </summary>
    /// <remarks>
    ///     版本：4.15.3.2
    ///     日期：2015年3月2日
    /// </remarks>
    public static class JsonSerializer
    {
        /// <summary>
        ///     将对象实例序列化为 JSON 数据文本
        /// </summary>
        /// <param name="instance">对象实例</param>
        /// <returns>返回实例的 JSON 数据</returns>
        public static string Serializer<T>(T instance)
        {
            if (instance == null) return string.Empty;
            
            using (var stream = new MemoryStream())
            {	
				var serializer = new DataContractJsonSerializer(typeof(T));
                serializer.WriteObject(stream, instance);
                var buffer = stream.ToArray();
                return Encoding.UTF8.GetString(buffer, 0, buffer.Length);
            }
        }

        /// <summary>
        ///     将 JSON 数据文本反序列化为对象实例
        /// </summary> 
        /// <param name="json">JSON 数据文本</param>
        /// <returns>返回类型实例，当 JSON 为 null 或 empty 时，返回 T 类型默认实例</returns>
        public static T Deserializer<T>(string json)
        {
            if (string.IsNullOrEmpty(json)) return Activator.CreateInstance<T>();

            using (var memory = new MemoryStream(Encoding.UTF8.GetBytes(json)))
            {
                var serializer = new DataContractJsonSerializer(typeof(T));
                return (T)serializer.ReadObject(memory);
            } 
        }
    }

## RESTful数据请求

### Restful 

	/// <summary>
    ///     定义 RESTful 风格的资源请求，支持 HTTP 谓词 GET、POST、PUT、DELETE 等
    ///     @ 2010-2015 Matteo Swift
    /// </summary>
    /// <remarks>
    ///     版本：4.15.3.2
    ///     日期：2015年3月2日
    /// </remarks>
    public static partial class Restful
    {
        private static void OnCallback<T>(Action<T> onCallback, T arg)
        {
            if (onCallback != null)
            {
                Deployment.Current.Dispatcher.BeginInvoke(() => onCallback(arg));
            }
        }

        #region 内部处理

        private static void AsyncCallback(WebRequest request, IAsyncResult asyncResult, Action<Callback> onCallback, string data, string method)
        {
            var result = new Callback {UserState = asyncResult.AsyncState, RequestUri = request.RequestUri, Method = method};
            if (string.IsNullOrEmpty(data))
            {
                try
                {
                    using (var response = (HttpWebResponse) request.EndGetResponse(asyncResult))
                    {
                        try
                        {
                            using (var stream = response.GetResponseStream())
                            {
                                if (stream.Length != 0)
                                {
                                    var buffer = new byte[stream.Length];
                                    stream.Read(buffer, 0, buffer.Length);
                                    result.Data = JsonSerializer.Unserializer<HttpResult>(Encoding.UTF8.GetString(buffer, 0, buffer.Length));
                                }
                            }
                        }
                        catch (Exception ex)
                        {
                            result.Error = ex;
                        }
                    }
                }
                catch (Exception ex)
                {
                    result.Error = ex;
                }

                OnCallback(onCallback, result);
            }

            if (!string.IsNullOrEmpty(data))
            {
                using (var stream = request.EndGetRequestStream(asyncResult))
                {
                    var buffer = Encoding.UTF8.GetBytes(data);
                    stream.Write(buffer, 0, buffer.Length);
                }

                request.BeginGetResponse(iAsyncResult =>
                {
                    try
                    {
                        using (var response = (HttpWebResponse) request.EndGetResponse(iAsyncResult))
                        {
                            using (var stream = response.GetResponseStream())
                            {
                                if (stream.Length != 0)
                                {
                                    var buffer = new byte[stream.Length];
                                    stream.Read(buffer, 0, buffer.Length);
                                    result.Data = JsonSerializer.Unserializer<HttpResult>(Encoding.UTF8.GetString(buffer, 0, buffer.Length));
                                }
                            }
                        }
                    }
                    catch (Exception ex)
                    {
                        result.Error = ex;
                    }

                    OnCallback(onCallback, result);
                }, null);
            }
        }

        private static void TryRequest(string method, string uri, string data = null, Action<Callback> onCallback = null, object state = null)
        {
            try
            {
                var request = WebRequestCreator.ClientHttp.Create(new Uri(uri, UriKind.RelativeOrAbsolute));
                request.Method = method;
                if (!string.IsNullOrEmpty(data))
                {
                    request.ContentType = "application/json";
                    request.BeginGetRequestStream(iAsyncResult => AsyncCallback(request, iAsyncResult, onCallback, data, method), state);
                }
                else
                {
                    request.BeginGetResponse(iAsyncResult => AsyncCallback(request, iAsyncResult, onCallback, null, method), state);
                }
            }
            catch (Exception ex)
            {
                OnCallback(onCallback, new Callback {Error = ex, UserState = state, RequestUri = new Uri(uri, UriKind.RelativeOrAbsolute), Method = method});
            }
        }

        private static void AsyncCallback<T>(WebRequest request, IAsyncResult asyncResult, Action<Callback<T>> onCallback, string data, string method)
        {
            var result = new Callback<T> {UserState = asyncResult.AsyncState, RequestUri = request.RequestUri, Method = method};
            if (string.IsNullOrEmpty(data))
            {
                try
                {
                    using (var response = (HttpWebResponse) request.EndGetResponse(asyncResult))
                    {
                        try
                        {
                            using (var stream = response.GetResponseStream())
                            {
                                if (stream.Length != 0)
                                {
                                    var buffer = new byte[stream.Length];
                                    stream.Read(buffer, 0, buffer.Length);
                                    result.Data = JsonSerializer.Unserializer<T>(Encoding.UTF8.GetString(buffer, 0, buffer.Length));
                                }
                            }
                        }
                        catch (Exception ex)
                        {
                            result.Error = ex;
                        }
                    }
                }
                catch (Exception ex)
                {
                    result.Error = ex;
                }

                OnCallback(onCallback, result);
            }

            if (!string.IsNullOrEmpty(data))
            {
                using (var stream = request.EndGetRequestStream(asyncResult))
                {
                    var buffer = Encoding.UTF8.GetBytes(data);
                    stream.Write(buffer, 0, buffer.Length);
                }

                request.BeginGetResponse(iAsyncResult =>
                {
                    try
                    {
                        using (var response = (HttpWebResponse) request.EndGetResponse(iAsyncResult))
                        {
                            using (var stream = response.GetResponseStream())
                            {
                                if (stream.Length != 0)
                                {
                                    var buffer = new byte[stream.Length];
                                    stream.Read(buffer, 0, buffer.Length);
                                    result.Data = JsonSerializer.Unserializer<T>(Encoding.UTF8.GetString(buffer, 0, buffer.Length));
                                }
                            }
                        }
                    }
                    catch (Exception ex)
                    {
                        result.Error = ex;
                    }

                    OnCallback(onCallback, result);
                }, null);
            }
        }

        private static void TryRequest<T>(string method, string uri, string data = null, Action<Callback<T>> onCallback = null, object state = null)
        {
            try
            {
                var request = WebRequestCreator.ClientHttp.Create(new Uri(uri, UriKind.RelativeOrAbsolute));
                request.Method = method;
                if (!string.IsNullOrEmpty(data))
                {
                    request.ContentType = "application/json";
                    request.BeginGetRequestStream(iAsyncResult => AsyncCallback(request, iAsyncResult, onCallback, data, method), state);
                }
                else
                {
                    request.BeginGetResponse(iAsyncResult => AsyncCallback(request, iAsyncResult, onCallback, null, method), state);
                }
            }
            catch (Exception ex)
            {
                OnCallback(onCallback, new Callback<T> {Error = ex, UserState = state, RequestUri = new Uri(uri, UriKind.RelativeOrAbsolute), Method = method});
            }
        }

        #endregion

        /// <summary>
        ///     WebRequest [GET] 请求远程资源数据，所有参数均通过 URI 传递，因此请注意 URI 长度限制
        /// </summary>
        /// <param name="uri">请求 URI 地址</param>
        /// <param name="onCallback">服务端响应请求后客户端回调函数</param>
        /// <param name="state">An object containing state information for this asynchronous request.</param>
        public static void Get(string uri, Action<Callback> onCallback = null, object state = null)
        {
            var request = WebRequest.Create(uri);
            try
            {
                request.BeginGetResponse(iAsyncResult => AsyncCallback(request, iAsyncResult, onCallback, null, "GET"), state);
            }
            catch (Exception ex)
            {
                OnCallback(onCallback, new Callback {Error = ex, UserState = state, RequestUri = new Uri(uri, UriKind.RelativeOrAbsolute), Method = "GET"});
            }
        }

        /// <summary>
        ///     WebRequest [GET] 请求远程资源数据，所有参数均通过 URI 传递，因此请注意 URI 长度限制
        /// </summary>
        /// <param name="uri">请求 URI 地址</param>
        /// <param name="onCallback">服务端响应请求后客户端回调函数</param>
        /// <param name="state">An object containing state information for this asynchronous request.</param>
        public static void Get<T>(string uri, Action<Callback<T>> onCallback = null, object state = null)
        {
            var request = WebRequest.Create(uri);
            try
            {
                request.BeginGetResponse(iAsyncResult => AsyncCallback(request, iAsyncResult, onCallback, null, "GET"), state);
            }
            catch (Exception ex)
            {
                OnCallback(onCallback, new Callback<T> {Error = ex, UserState = state, RequestUri = new Uri(uri, UriKind.RelativeOrAbsolute), Method = "GET"});
            }
        }

        /// <summary>
        ///     WebRequest [POST] 请求远程资源数据，参数可通过 Body 传递且无长度限制
        /// </summary>
        /// <param name="uri">请求 URI 地址</param>
        /// <param name="onCallback">服务端响应请求后客户端回调函数</param>
        /// <param name="body">通过 HTTP Body 传递数据内容，JSON 格式数据</param>
        /// <param name="state">An object containing state information for this asynchronous request.</param>
        public static void Post(string uri, string body = null, Action<Callback> onCallback = null, object state = null)
        {
            TryRequest("POST", uri, body, onCallback, state);
        }

        /// <summary>
        ///     WebRequest [POST] 请求远程资源数据，参数可通过 Body 传递且无长度限制
        /// </summary>
        /// <param name="uri">请求 URI 地址</param>
        /// <param name="onCallback">服务端响应请求后客户端回调函数</param>
        /// <param name="body">通过 HTTP Body 传递数据内容，JSON 格式数据</param>
        /// <param name="state">An object containing state information for this asynchronous request.</param>
        public static void Post<T>(string uri, string body = null, Action<Callback<T>> onCallback = null, object state = null)
        {
            TryRequest("POST", uri, body, onCallback, state);
        }

	 	/// <summary>
        ///     WebRequest [POST] 请求远程资源数据，参数可通过 Body 传递且无长度限制
        /// </summary>
        /// <param name="uri">请求 URI 地址</param>
        /// <param name="onCallback">服务端响应请求后客户端回调函数</param>
        /// <param name="body">通过 HTTP Body 传递数据内容，<see cref="V"/> 数据实例，非 JSON 格式数据</param>
        /// <param name="state">An object containing state information for this asynchronous request.</param>
        public static void Post<V, T>(string uri, V body, Action<Callback<T>> onCallback = null, object state = null)
        {
            TryRequest("POST", uri, JsonSerializer.Serializer(body), onCallback, state);
        }

        /// <summary>
        ///     WebRequest [PUT] 请求远程资源数据，参数可通过 Body 传递且无长度限制
        /// </summary>
        /// <param name="uri">请求 URI 地址</param>
        /// <param name="onCallback">服务端响应请求后客户端回调函数</param>
        /// <param name="body">通过 HTTP Body 传递数据内容，JSON 格式数据</param>
        /// <param name="state">An object containing state information for this asynchronous request.</param>
        public static void Put(string uri, string body = null, Action<Callback> onCallback = null, object state = null)
        {
            TryRequest("PUT", uri, body, onCallback, state);
        }


        /// <summary>
        ///     WebRequest [PUT] 请求远程资源数据，参数可通过 Body 传递且无长度限制
        /// </summary>
        /// <param name="uri">请求 URI 地址</param>
        /// <param name="onCallback">服务端响应请求后客户端回调函数</param>
        /// <param name="body">通过 HTTP Body 传递数据内容，JSON 格式数据</param>
        /// <param name="state">An object containing state information for this asynchronous request.</param>
        public static void Put<T>(string uri, string body = null, Action<Callback<T>> onCallback = null, object state = null)
        {
            TryRequest("PUT", uri, body, onCallback, state);
        }
		
		/// <summary>
        ///     WebRequest [PUT] 请求远程资源数据，参数可通过 Body 传递且无长度限制
        /// </summary>
        /// <param name="uri">请求 URI 地址</param>
        /// <param name="onCallback">服务端响应请求后客户端回调函数</param>
        /// <param name="body">通过 HTTP Body 传递数据内容，<see cref="V"/> 类型数据实例，非 JSON 格式数据</param>
        /// <param name="state">An object containing state information for this asynchronous request.</param>
        public static void Put<V, T>(string uri, V body, Action<Callback<T>> onCallback = null, object state = null)
        {
            TryRequest("PUT", uri, JsonSerializer.Serializer(body), onCallback, state);
        }

        /// <summary>
        ///     WebRequest [DELETE] 请求远程资源数据
        /// </summary>
        /// <param name="uri">请求 URI 地址</param>
        /// <param name="onCallback">服务端响应请求后客户端回调函数</param>
        /// <param name="state">An object containing state information for this asynchronous request.</param>
        public static void Delete(string uri, Action<Callback> onCallback = null, object state = null)
        {
            TryRequest("DELETE", uri, null, onCallback, state);
        }

        /// <summary>
        ///     WebRequest [DELETE] 请求远程资源数据
        /// </summary>
        /// <param name="uri">请求 URI 地址</param>
        /// <param name="onCallback">服务端响应请求后客户端回调函数</param>
        /// <param name="state">An object containing state information for this asynchronous request.</param>
        public static void Delete<T>(string uri, Action<Callback<T>> onCallback = null, object state = null)
        {
            TryRequest("DELETE", uri, null, onCallback, state);
        }
    }

## 终端示例代码

1. 依赖上述 HttpResult、Callback、Restful 等类型以及 MVVM Light 的 ObservableObject 类型
2. 定义 UserModel ，用于封装用户相关信息，参考属性注释。
3. 定义 UserViewModel，用于封装相关操作，如获取用户列表信息。


### UserModel & UserViewModel 

	/// <summary>
    ///     用户信息
    /// </summary>
    public class UserModel : ObservableObject
    {
        private string _userID = default(string);

        /// <summary>
        ///     用户登录名
        /// </summary>
        public string UserID
        {
            get { return this._userID; }
            set { this.Set("UserID", ref this._userID, value); }
        }

        private string _userName = default(string);

        /// <summary>
        ///     用户姓名
        /// </summary>
        public string UserName
        {
            get { return this._userName; }
            set { this.Set("UserName", ref this._userName, value); }
        }

        private string _password = default(string);

        /// <summary>
        ///     用户密码
        /// </summary>
        public string Password
        {
            get { return this._password; }
            set { this.Set("Password", ref this._password, value); }
        }

        private string _iv = default(string);

        /// <summary>
        ///     加密向量
        /// </summary>
        public string IV
        {
            get { return this._iv; }
            set { this.Set("IV", ref this._iv, value); }
        }

        private string _gender = default(string);

        /// <summary>
        ///     用户性别，中文字符 男 或 女
        /// </summary>
        public string Gender
        {
            get { return this._gender; }
            set { this.Set("Gender", ref this._gender, value); }
        }

        private string _birthday = default(string);

        /// <summary>
        ///     用户生日，格式 1985-11-10
        /// </summary>
        public string Birthday
        {
            get { return this._birthday; }
            set { this.Set("Birthday", ref this._birthday, value); }
        }

        private string _phone = default(string);

        /// <summary>
        ///     用户手机号
        /// </summary>
        public string Phone
        {
            get { return this._phone; }
            set { this.Set("Phone", ref this._phone, value); }
        }

        private string _email = default(string);

        /// <summary>
        ///     用户邮箱
        /// </summary>
        public string Email
        {
            get { return this._email; }
            set { this.Set("Email", ref this._email, value); }
        }

        private string _attachedInfo = default(string);

        /// <summary>
        ///     附加信息，通常为json数据
        /// </summary>
        public string AttachedInfo
        {
            get { return this._attachedInfo; }
            set { this.Set("AttachedInfo", ref this._attachedInfo, value); }
        }

        private string _title = default(string);

        /// <summary>
        ///     所属组织内的职称
        /// </summary>
        public string Title
        {
            get { return this._title; }
            set { this.Set("Title", ref this._title, value); }
        }

        private string _organizationName = default(string);

        /// <summary>
        ///     所属组织的名称
        /// </summary>
        public string OrganizationName
        {
            get { return this._organizationName; }
            set { this.Set("OrganizationName", ref this._organizationName, value); }
        }

        private string _organizationID = default(string);

        /// <summary>
        ///     所属组织的编码ID
        /// </summary>
        public string OrganizationID
        {
            get { return this._organizationID; }
            set { this.Set("OrganizationID", ref this._organizationID, value); }
        }
    }

	/// <summary>
    ///     用户管理信息
    /// </summary>
    public class UserViewModel : ViewModelBase
    {
 
		private ObservableCollection<UserModel> _users = default(ObservableCollection<UserModel>);

        /// <summary>
        ///     系统用户列表
        /// </summary>
        public ObservableCollection<UserModel> Users
        {
            get { return this._users; }
            set { this.Set("Users", ref this._users, value); }
        }

		private string _bswWebApi;

		public UserViewModel()
		{
			this._bswWebApi = Application.Current.GetApiBasicUri("bsw");
			if (!IsInDesignMode)
            {
                this.Initial();
            }
		}

		private void Initial()
		{
			this.IsBusy = true;
			this.BusyContent = "开始加载用户数据，请等待...";
			var uri = string.Format("{0}/users?format=json", this._bswWebApi);
			Restful.Get<Callbakc<ObservableCollection<UserModel>>>(uri, result=>
			{
				if(!result.HasError)
				{
					//this.Debug("BSW",result); 
					this.Users = result.Data.Value;
					//以下是服务端响应信息
					//result.Data.HasError
					//result.Data.Message
					//result.Data.ExecSec
				}
				else
				{
					HtmlPage.Window.Alert(result.Error.Message);
				}
			},null);
		}
	}