# JavaScript

## Fehlerbehandlung bei asynchronen Aufrufen
Grundsätzlich sind zwei Scenarien zu beachten: 

1. Der Fehler tritt nicht während der eigentlichen, asynchronen Ausführung der Funktion/des Programms auf. Stattdessen tritt der Fehler während eines synchronen Aufrufs auf, bei dem lediglich eine Promise zurückgegeben wird. Dieser Fall ist eigentlich gar keine Besonderheit.

   ```js
   const doWork = () => {
        throw new Error('Sync Error!');
        return new Promise(() => {
            setTimeout(() => {
                console.log('Success!');
            }, 1000);
        });
   };
   ```
   
2. Der Fehler tritt während der asynchronen Ausführung auf:

   ```js
   const doWork = async () => {
       return new Promise(() => {
           throw new Error('Sync Error!');
           console.log('Success!');
       }, 1000);
   };
   ```

## Bundle-/Modulformate

### ES Module

### CommonJS

### IIFE

### UMD
