---
title: "Testing Post"
date: 2020-03-30T19:57:28+01:00
draft: true
---

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum


```csharp
using Moq;
using Xunit;

namespace BasicAAATestExample
{
    public interface IUser
    {
        string GetFullname();
        string Firstname { get; set; }
        string Lastname { get; set; }
    }

    public class User : IUser
    {
        public string Firstname { get; set; }

        public string Lastname { get; set; }

        public string GetFullname()
        {
            return $"{Firstname} {Lastname}";
        }
    }

    public class Notify
    {
        private IUser _user;

        public Notify(IUser user) => _user = user;

        public string GetMessage() => $"{_user.GetFullname()} has been notified";
    }

    public class NotifyTests
    {
        [Theory]
        [InlineData("Garrard", "Kitchen", "Garrard Kitchen has been notified")]
        [InlineData("Charles", "Kitchen", "Charles Kitchen has been notified")]
        public void GivenGetMessageIsCalled_WhenFirstAndLastNameExist_ThenReturnsANotificationMessage(string firstname,
            string lastname, string expected)
        {
            // arrange
            var mockUser = new Mock<IUser>();
            mockUser.Setup(x => x.GetFullname()).Returns($"{firstname} {lastname}");
            var sut = new Notify(mockUser.Object);

            // act
            string message = sut.GetMessage();

            // assert
            Assert.Equal(expected, message);
            mockUser.Verify(x => x.GetFullname(), Times.Once);
        }
    }
}
```