
Basic example of using GetAsync to make a HTTP request.

        [Fact]
        public async Task SimpleGet()
        {
            var httpClient = new HttpClient();

            var response = await httpClient.GetAsync("http://www.nuget.org");

            Assert.Equal(HttpStatusCode.OK, response.StatusCode);
            Assert.Equal("text/html",response.Content.Headers.ContentType.MediaType);
        }
