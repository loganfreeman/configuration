inherit from EventEmitter
---
```node
util.inherits( Deepstream, EventEmitter );
```
Combine events
---
```node
exports.combineEvents = function( emitters, event, callback ) {
    var i,
        count = 0,
        increment = function() {
            count++;
            
            if( count === emitters.length ) {
                callback();
            }
        };
        
  for( i = 0; i < emitters.length; i++ ) {
      emitters[ i ].once( event, increment );
  }  
};
```
