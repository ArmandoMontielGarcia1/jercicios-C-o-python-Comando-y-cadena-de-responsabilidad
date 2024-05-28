# Ejercicios-C-o-python-Comando-y-cadena-de-responsabilidad
# Nombre: Armando Montiel Garcia #20210602
# Descripción de la Actividad
Objetivo: Utilizar los patrones de diseño Comando y Cadena de Responsabilidad para desarrollar una aplicación móvil de edición de imágenes que permita aplicar filtros y ajustes de forma secuencial y controlada.
# CODIGO DEL PROGRAMA

using System;
namespace ConsoleApp1
{
    public interface ICommand
    {
        void Execute();
        void Undo();
    }

    // Comando concreto para ajustar el brillo
    public class BrightnessCommand : ICommand
    {
        private readonly Image _image;
        private readonly int _amount;

        public BrightnessCommand(Image image, int amount)
        {
            _image = image;
            _amount = amount;
        }

        public void Execute()
        {
            _image.AdjustBrightness(_amount);
        }

        public void Undo()
        {
            _image.AdjustBrightness(-_amount);
        }
    }

    // Comando concreto para ajustar el contraste
    public class ContrastCommand : ICommand
    {
        private readonly Image _image;
        private readonly int _amount;

        public ContrastCommand(Image image, int amount)
        {
            _image = image;
            _amount = amount;
        }

        public void Execute()
        {
            _image.AdjustContrast(_amount);
        }

        public void Undo()
        {
            _image.AdjustContrast(-_amount);
        }
    }

    // Gestor de comandos
    public class CommandManager
    {
        private readonly Stack<ICommand> _commands = new Stack<ICommand>();
        private readonly Stack<ICommand> _undoneCommands = new Stack<ICommand>();

        public void ExecuteCommand(ICommand command)
        {
            command.Execute();
            _commands.Push(command);
        }

        public void Undo()
        {
            if (_commands.Count > 0)
            {
                ICommand command = _commands.Pop();
                command.Undo();
                _undoneCommands.Push(command);
            }
        }

        public void Redo()
        {
            if (_undoneCommands.Count > 0)
            {
                ICommand command = _undoneCommands.Pop();
                command.Execute();
                _commands.Push(command);
            }
        }
    }

    // Clase de imagen que será editada
    public class Image
    {
        // Métodos para ajustar el brillo y el contraste
        public void AdjustBrightness(int amount)
        {
            Console.WriteLine($"Adjusting brightness by {amount} units.");
        }

        public void AdjustContrast(int amount)
        {
            Console.WriteLine($"Adjusting contrast by {amount} units.");
        }
    }

    // Cadena de responsabilidad para aplicar filtros
    public class FilterChain
    {
        private readonly List<Func<Action, Action>> _filters = new List<Func<Action, Action>>();

        public void AddFilter(Func<Action, Action> filter)
        {
            _filters.Add(filter);
        }

        public Action GetProcessingChain()
        {
            Action result = () => { };

            foreach (var filter in _filters)
            {
                result = filter(result);
            }

            return result;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // Crear una imagen
            Image image = new Image();

            // Crear gestor de comandos
            CommandManager commandManager = new CommandManager();

            // Crear comandos concretos
            ICommand brightnessCommand = new BrightnessCommand(image, 10);
            ICommand contrastCommand = new ContrastCommand(image, 20);

            // Ejecutar comandos
            commandManager.ExecuteCommand(brightnessCommand);
            commandManager.ExecuteCommand(contrastCommand);

            // Deshacer último comando
            commandManager.Undo();

            // Rehacer último comando
            commandManager.Redo();

            // Ejemplo de cadena de responsabilidad para filtros
            FilterChain filterChain = new FilterChain();
            filterChain.AddFilter(next => () =>
            {
                Console.WriteLine("Applying filter 1");
                next();
            });
            filterChain.AddFilter(next => () =>
            {
                Console.WriteLine("Applying filter 2");
                next();
            });

            Action processingChain = filterChain.GetProcessingChain();
            processingChain();

            Console.ReadLine();
        }
    }
}
![image](https://github.com/ArmandoMontielGarcia1/jercicios-C-o-python-Comando-y-cadena-de-responsabilidad/assets/144396511/2c5ed204-48f1-4139-b92d-33cb60be523c)
