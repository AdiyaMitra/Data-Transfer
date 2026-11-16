Console.WriteLine("Enter the first number:");
            double num1 = Convert.ToDouble(Console.ReadLine());

            Console.WriteLine("Enter the second number:");
            double num2 = Convert.ToDouble(Console.ReadLine());

            Console.WriteLine("Choose an operation (+, -, *, /):");
            string operation = Console.ReadLine();

            if (operation == "+")
            {
                Console.WriteLine($"Result: {num1 + num2}");
            }
            else if (operation == "-")
            {
                Console.WriteLine($"Result: {num1 - num2}");
            }
            else if (operation == "*")
            {
                Console.WriteLine($"Result: {num1 * num2}");
            }
            else if (operation == "/")
            {
                if (num2 != 0)
                {
                    Console.WriteLine($"Result: {num1 / num2}");
                }
                else
                {
                    Console.WriteLine("Error: Division by zero is not allowed.");
                }
            }
            else
            {
                Console.WriteLine("Invalid operation. Please choose +, -, *, or /.");
            }
