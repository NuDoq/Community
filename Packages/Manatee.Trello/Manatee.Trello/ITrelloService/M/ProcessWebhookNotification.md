Get the body via

Stream req = Request.InputStream;
req.Seek(0, SeekOrigin.Begin);
jsonBody = new StreamReader(req).ReadToEnd();
