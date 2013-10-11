IsAny can also be used to catch all values and save them for later comparison/checks, when used in combination with Callback.

For example, given this interface:

    public interface IDocumentStore
    {
        void Delete(int id);
        string Read(int id);
        void Save(int id, string document);
    }

You could set it up to store the id and document values passed to the Save method with the following code:

            var store = new Mock<IDocumentStore>();
            var savedId = 0;
            var savedDoc = "";

            store.Setup(x => x.Save(It.IsAny<int>(), It.IsAny<string>()))
                .Callback<int, string>((id, document) =>
                {
                    savedId = id;
                    savedDoc = document;
                });
