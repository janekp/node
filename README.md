evented I/O for v8 javascript + dynamic modules
===

### Why:

It allows to use NodeJS as a static/dynamic library and lazily load your own native extensions in the main binary.

### Example:

main.cc:

	#include <node.h>
	
	namespace demo {
		v8::Handle<v8::Value> Message(const v8::Arguments &args) {
			v8::HandleScope scope;
			v8::Local<v8::String> message = v8::String::New("Hello World!");
			
			return scope.Close(message);
		}
		
		void Initialize(v8::Handle<v8::Object> target) {
			v8::HandleScope scope;
			
			NODE_SET_METHOD(target, "message", Message);
		}
	}
	
	NODE_MODULE(demo, demo::Initialize);
	
	int main(int argc, char *argv[]) {
		node::register_dynamic_module(&demo_module);
		
		return node::Start(argc, argv);
	}

main.js:

    console.log(require('demo').message());
