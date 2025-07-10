(https://www.youtube.com/watch?v=dRpRiJehdvc)

(https://metanit.com/sharp/patterns/3.2.php)

[Все паттерны в куче](https://javarush.com/groups/posts/496-patternih-proektirovanija-v-java)


Паттерн "Наблюдатель" (Observer) представляет поведенческий шаблон проектирования, который использует отношение "один ко многим". В этом отношении есть один наблюдаемый объект и множество наблюдателей

<img width="1389" height="556" alt="image" src="https://github.com/user-attachments/assets/831fa3c0-ce79-4e7e-ba09-a5328b1a30ab" />

## Когда использовать? 
- когда система состоит из множества классов, объекты которых должны находиться в согласованных состояниях
- когда общая схема взаимодействия объектов предполагает две стороны: одна рассылает сообщения, а другая получает сообщения и реагирует на них
- когда существует один объект, рассылающий сообщения, и множества подписчиков, которые получают сообщения.

```
import java.util.ArrayList;
import java.util.List;
interface Observer {
    void event(List<String> strings);
}
class University {
    private List<Observer> observers = new ArrayList<Observer>();
    private List<String> students = new ArrayList<String>();
    public void addStudent(String name) {
        students.add(name);
        notifyObservers();
    }
    public void removeStudent(String name) {
        students.remove(name);
        notifyObservers();
    }
    public void addObserver(Observer observer){
        observers.add(observer);
    }
    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }
    public void notifyObservers(){
        for (Observer observer : observers) {
            observer.event(students);
        }
    }
}
class Director implements Observer {
    public void event(List<String> strings) {
        System.out.println("The list of students has changed: " + strings);
    }
}

public class ObserverTest {//тест
    public static void main(String[] args) {
        University university = new University();
        Director director = new Director();
        university.addStudent("Vaska");
        university.addObserver(director);
        university.addStudent("Anna");
        university.removeStudent("Vaska");
    }
}
```
