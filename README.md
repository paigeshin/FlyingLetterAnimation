# FlyingLetterAnimation

```swift
struct ContentView: View {
    
    @State var titles = [
        "Clear your mind from",
        "Clear your mind from",
        "Clear your mind from",
    ]
    
    @State var subtitles = [
        "Negativity - Stress - Anxiety",
        "Prepare your mind for sweet dreams",
        "Healthy mind - btter sleep - well being"
    ]
    
    @State var currentIndex = 2
    @State var titleText: [TextAnimation] = []
    
    @State var subtitleAnimation = false
    @State var endAnimation = false
    
    var body: some View {
        ZStack {
            
            Color.black
         
            // Chaning Image
            ForEach(0...3, id: \.self) { index in
                Image("Pic\(index)")
                    .resizable()
                    .aspectRatio(contentMode: .fill)
                    .opacity(self.currentIndex == (index - 1) ? 1 : 0)
            }
            
            // Bottom Content ...
            VStack(spacing: 20) {
                
                HStack(spacing: 0) {
                    ForEach(self.titleText) { text in
                        Text(text.text)
                            .offset(y: text.offset)
                    }
                    .font(.largeTitle.bold())
                }
                .offset(y: self.endAnimation ? -100 : 0)
                .opacity(self.endAnimation ? 0 : 1)
                
                Text(self.subtitles[self.currentIndex])
                    .opacity(0.7)
                    .offset(y: !self.subtitleAnimation ? 80 : 0)
                    .offset(y: self.endAnimation ? -100 : 0)
                    .opacity(self.endAnimation ? 0 : 1)
                
            } //: VSTACK
            .padding()
            .frame(maxWidth: .infinity, maxHeight: .infinity)
            
            
        }
        .onAppear(perform: {
            self.currentIndex = 0
        })
        .onChange(of: self.currentIndex) { currentIndex in
            self.getSplitText(text: self.titles[currentIndex]) {
                // removing the current one and updaing index...
                withAnimation(.easeIn(duration: 1)) {
                    self.endAnimation.toggle()
                }
                
                
                // Updatin Index...
                DispatchQueue.main.asyncAfter(deadline: .now() + 0.9) {
                    // Removing Title text...
                    self.titleText.removeAll()
                    self.subtitleAnimation.toggle()
                    self.endAnimation.toggle()
                    
                    // updating index...
                    withAnimation(.easeIn(duration: 0.6)) {
                        if self.currentIndex < self.titles.count - 1 {
                            self.currentIndex += 1
                        } else {
                            // setting back to 0...
                            // so it will be endless loop...
                            self.currentIndex = 0
                        }
                    }
               
                }
                
            }
        }
    }
    
    // Spliting Text into array of characters and animating it..
    // Completion handler to inform the snimation completion...
    func getSplitText(text: String, completion: @escaping() -> Void) {
        
        for (index, character) in text.enumerated() {
            
            // appending into title text..
            self.titleText.append(TextAnimation(text: String(character)))
            
            // with time delay setting text offset to 0 ...
            DispatchQueue.main.asyncAfter(deadline: .now() + Double(index) * 0.03) {
                withAnimation(.easeInOut(duration: 0.5)) {
                    self.titleText[index].offset = 0
                }
            }

        }
        
        DispatchQueue.main.asyncAfter(deadline: .now() + Double(text.count) * 0.02) {
            withAnimation(.easeInOut(duration: 0.5)) {
                self.subtitleAnimation.toggle()
            }
            
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.95) {
                completion()
            }
            
        }
        
    }
    
    
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

struct TextAnimation: Identifiable {
    
    var id = UUID().uuidString
    var text: String
    var offset: CGFloat = 100
    
}

```
