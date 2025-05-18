코드
#include <SFML/Graphics.hpp>
#include <cmath>

sf::Color HSVtoRGB(float H, float S, float V, sf::Uint8 A=120) {
    float C = V * S, X = C * (1 - fabs(fmod(H/60,2)-1)), m = V - C;
    float r,g,b;
    if(H<60)      r=C, g=X, b=0;
    else if(H<120)r=X, g=C, b=0;
    else if(H<180)r=0, g=C, b=X;
    else if(H<240)r=0, g=X, b=C;
    else if(H<300)r=X, g=0, b=C;
    else          r=C, g=0, b=X;
    return sf::Color((r+m)*255,(g+m)*255,(b+m)*255,A);
}

int main() {
    sf::RenderWindow w({800,600}, "Rainbow Follow");
    sf::Texture t; if(!t.loadFromFile("jcshim.jpg")) return -1;
    sf::Sprite s(t);
    auto size = t.getSize();
    s.setOrigin(size.x/2.f,size.y/2.f);
    sf::RectangleShape overlay({(float)size.x,(float)size.y});
    overlay.setOrigin(size.x/2.f,size.y/2.f);

    sf::Vector2f pos(w.getSize().x/2.f,w.getSize().y/2.f);
    sf::Clock clock;
    float speed = 0.001f;

    while(w.isOpen()) {
        sf::Event e;
        while(w.pollEvent(e))
            if(e.type==sf::Event::Closed) w.close();

        auto mpos = (sf::Vector2f)sf::Mouse::getPosition(w);
        pos += (mpos - pos) * speed;

        float hue = fmod(clock.getElapsedTime().asSeconds()*36.f,360.f);
        overlay.setFillColor(HSVtoRGB(hue,1.f,1.f));
        s.setPosition(pos);
        overlay.setPosition(pos);

        w.clear();
        w.draw(s);
        w.draw(overlay);
        w.display();
    }
}

