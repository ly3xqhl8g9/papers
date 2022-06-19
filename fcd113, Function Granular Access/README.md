# Function Granular Access


Extended programs and especially programs which are intended for extension and composition such as libraries require special care in terms of security.

A granular access restriction for functions is meant as a way to prevent at write/compile/run-time access to filesystem, network, or even the function's exterior.

Below, a possible exemplification for `TypeScript`:


``` typescript
interface FunctionGranularAccess {
    network?: boolean | string | string[]; // allows network access for all (boolean), for one (string), for many (string[])
    filesystem?: boolean | string | string[]; // allows filesystem access for all (boolean), for one (string), for many (string[])
    exterior?: boolean; // allows access to functions/variables outside of the function's scope
    environment?: boolean; // allows access to process environment
    imports?: boolean; // allows the function to import dynamically
    dependencies?: string[] // specify the dependencies which can be used by the function
}
```


Example of a simple `add`er function without any access:

``` typescript
/**
 * The function adds the `first` value with the `second` value.
 *
 * @param first
 * @param second
 * @returns
 */
function add {
    /**
     * The function and all the calls it will make
     * cannot access the network or the filesystem
     * or any entity outside it's scope.
     */
    network: false,
    filesystem: false,
    exterior: false,
} (
    first: number,
    second: number,
): number {
    return first + second;
}
```


Example of a function which throws an error at any `filesystem` call:

``` typescript
import fs from 'fs';


function throwsError {
    filesystem: false,
} () {
    fs.createReadStream('error');
}
```


Example of a function which uses an object for `Function Granular Access`:

``` typescript
const noNetworkAccess: FunctionGranularAccess = {
    network: false,
};

function noNetwork {noNetworkAccess} () {
    return 'no network access';
}
```


Example of a `const` function:

``` typescript
const noNetwork {
    network: false,
} = () => {
    return 'no network access';
}
```


Example of a `class` with methods restricted by granular access:

``` typescript
class Example {
    hasNetworkAndFilesystem {
        network: true,
        filesystem: true,
    } (
        resource: string,
    ) {
        return 'network and filesystem';
    }

    noNetwork { network: false } () {
        return 'no network';
    }
}

const example = new Example();
example.hasNetworkAndFilesystem('/some-resource');
example.noNetwork();
```
