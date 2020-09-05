# Freenlance-Project

***Conversion From C# Code to UIPATH Robot***

* The C# code communicates to an API which is AI system and returns the result as per the user request and save the response to a local file.

C# Code :
---------

using System;
using Helpers;
using Newtonsoft.Json;

namespace AI_Communicator
{
    class Program
    {
        static void Main(string[] args)
        {
            string username = "UIPathTest"; //this is the username on our AI platform - please leave it as is for now
            string password = "U1PathT3st"; //this is the password on our AI platform - please leave it as is for not
            string modelId = "12a6230e-9c85-4e8f-9936-338c87c81b07"; //This tells the http request which model in our AI to use. Do not touch.
            string baseUrl = "https://sue3testbetaapi.azurewebsites.net/api"; //The endpoint for our urls
           
            //user inputs
            int param1 = 200; //This will be a number from the usr interface between 0 and 50000. Prompt: "Please enter pages per month"
            int param2 = 20; //This will be a number from the user interface between 0 and 100. Prompt: "Please enter toner level"
            string param3 = "No"; ; //This will be a string input from the user interface either "No" or "Ye"s. Prompt: "Is there a spare - Yes/No?"

            //login to get a token
            string endpoint = baseUrl + "/usermanagement/login";
            string user = JsonConvert.SerializeObject(new Login { UserId = username, Password = password });
            var login = new RestClient(endpoint, HttpVerb.POST, "application/json; charset=UTF-8", user);
            LoginResult tokenResult = JsonConvert.DeserializeObject<LoginResult>(login.MakeRequest());
            string token = tokenResult.AuthorizationToken;

            //use token, modelID and parameters to query the system
            Console.WriteLine("Querying Model...");
            string postData = param1.ToString() + "," + param2.ToString() + "," + param3;
            endpoint = baseUrl + "/model/small-ask?modelGuid=" + modelId + "&version=" + "-1";
            var query = new RestClient(endpoint, HttpVerb.POST, "application/json; charset=UTF-8", (postData));
            query.SetAuthorisationToken(token);
            string queryResult = query.MakeRequest();

            Console.WriteLine("Result: AI says " + queryResult);
        }

        public class Login
        {
            public string UserId { get; set; }
            public string Password { get; set; }

            public Login()
            {

            }

        }

        public class LoginResult
        {
            public string UserId { get; set; }
            public string AuthorizationToken { get; set; }
            public bool IsError { get; set; }
            public string Result { get; set; }
            public string Error { get; set; }

            public object ValidatedPayload { get; set; }
        }
    }
}


