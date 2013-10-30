##Implementing IPlatformAdaptor

Because Budgie is a Portable ClassLibrary, it has no in-built way to generate a SHA1 hash for Twitter. It relies on you providing that code by implementing the `IPlatformAdaptor` interface.

Here are some example implementations:

###WPF/Windows Forms/Console

    // using System.Security.Cryptography;

    internal class DesktopPlatformAdaptor : IPlatformAdaptor
    {
        public string ComputeSha1Hash(string key, string buffer)
        {
            using (var hasher = new HMACSHA1(UTF8Encoding.UTF8.GetBytes(key)))
            {
                return Convert.ToBase64String(hasher.ComputeHash(UTF8Encoding.UTF8.GetBytes(buffer)));
            }
        }
    }

###Windows Store

    // using Windows.Security.Cryptography;
    // using Windows.Security.Cryptography.Core;

    internal class StorePlatformAdaptor : IPlatformAdaptor
    {
        public string ComputeSha1Hash(string key, string buffer)
        {
            var algorithm = MacAlgorithmProvider.OpenAlgorithm(MacAlgorithmNames.HmacSha1);
            var keymaterial = CryptographicBuffer.ConvertStringToBinary(key, BinaryStringEncoding.Utf8);
            var hmacKey = algorithm.CreateKey(keymaterial);

            return CryptographicBuffer.EncodeToBase64String(CryptographicEngine.Sign(
                hmacKey,
                CryptographicBuffer.ConvertStringToBinary(buffer, BinaryStringEncoding.Utf8)));
        }
    }
