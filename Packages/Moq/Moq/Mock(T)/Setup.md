If more than one setup is specified for the same method or property, and there is a conflict between the setups, the later setup wins and is the one that will be executed.

e.g.,

public interface IFoo {
  bool DoSomething(string value);       
}

/// <summary>
/// Demonstrates that the specific setups are applied even though we have an overall type setup due to ordering.
/// </summary>
[Test]
public void SetupOrder1() {
    var mock = new Mock<IFoo>();

    mock.Setup(foo => foo.DoSomething(It.IsAny<string>())).Returns(true);
    mock.Setup(foo => foo.DoSomething("ping")).Returns(false);
    mock.Setup(foo => foo.DoSomething(It.IsRegex("(blah)", RegexOptions.IgnoreCase | RegexOptions.Multiline))).Returns(false);

    Assert.Multiple(() => {
        //All setup conditions are applied as expected
        Assert.IsTrue(mock.Object.DoSomething("pong")); //no match
        Assert.IsFalse(mock.Object.DoSomething("ping")); //ping = expect false
        Assert.IsFalse(mock.Object.DoSomething("this string contains blah")); //contains blah = expect false
    });
}

/// <summary>
/// Demonstrates that the specific setups lose to the overall type setup.
/// </summary>
[Test]
public void SetupOrder2() {
    var mock = new Mock<IFoo>();

    mock.Setup(foo => foo.DoSomething("ping")).Returns(false);
    mock.Setup(foo => foo.DoSomething(It.IsRegex("(blah)", RegexOptions.IgnoreCase | RegexOptions.Multiline))).Returns(false);
    mock.Setup(foo => foo.DoSomething(It.IsAny<string>())).Returns(true);

    Assert.Multiple(() => {
        //Only the last setup - the overall type setup - is applied
        
        //Will pass
        Assert.IsTrue(mock.Object.DoSomething("pong")); //no match
        
        //Will fail
        Assert.IsFalse(mock.Object.DoSomething("ping")); //ping = expect false
        
        //Will fail
        Assert.IsFalse(mock.Object.DoSomething("this string contains blah")); //contains blah = expect false
    });
}
