---
tags:
  - gis
---

[Search](https://arcgismonitor.maps.arcgis.com/home/search.html?restrict=true&sortField=relevance&sortOrder=desc&searchTerm=tutorials#content)

## 🏛️ 1. ArcGIS Hub

### What is it?

ArcGIS Hub is a cloud-based engagement and collaboration platform built around your ArcGIS content. [ArcGIS Docs+1](https://doc.arcgis.com/en/hub/get-started/what-is-arcgis-hub-.htm?utm_source=chatgpt.com)  
It allows organizations (government, NGOs, companies, etc.) to:

- Share data, maps, apps publicly or internally
    
- Build websites (called _Hub sites_) to showcase projects or initiatives
    
- Collaborate with community members, volunteers, stakeholders
    
- Use tools like events, surveys, discussion boards, newsletters
    
- Track progress on goals or initiatives
    

There are **two license levels**:

- **Hub Basic** (comes with ArcGIS Online): you can create unlimited sites, manage content, share maps and apps. [ArcGIS Docs](https://doc.arcgis.com/en/hub/get-started/what-is-arcgis-hub-.htm?utm_source=chatgpt.com)
    
- **Hub Premium**: adds more engagement tools like project-management, initiatives, richer collaboration, community user accounts, discussions. [ArcGIS Docs+1](https://doc.arcgis.com/en/hub/get-started/what-is-arcgis-hub-.htm?utm_source=chatgpt.com)
    

---

### How it's used (use cases)

- **Open Data Portals**: make your GIS data accessible to the public (CSV, GeoJSON, KML, etc.).
    
- **Community Projects**: e.g. disaster response site, habitat restoration project site, volunteer sign-ups.
    
- **Transparency / Governance**: publish dashboards, planning maps, datasets.
    
- **Collaborative Initiatives**: combine maps, apps, surveys to engage stakeholders.
    

Examples: A city publishes its infrastructure data via a hub; a nonprofit runs a volunteer mapping campaign; a regional planning body runs a site showing ongoing development projects.

---

### How to get started

1. **Sign in** using your ArcGIS Online account → go to Hub. [ArcGIS Docs](https://doc.arcgis.com/en/hub/get-started/sign-in.htm?utm_source=chatgpt.com)
    
2. **Create a site**:
    
    - Use the “Create → Site” option. [ArcGIS Docs](https://doc.arcgis.com/en/hub/sites/create-a-site.htm?utm_source=chatgpt.com)
        
    - Pick a title, subdomain, choose theme/layout.
        
    - Add “cards” to show maps, apps, images, text.
        
    - Configure a **catalog**: which content items (maps, layers) are visible on the site. [ArcGIS Docs](https://doc.arcgis.com/en/hub/sites/create-a-site.htm?utm_source=chatgpt.com)
        
3. **Set sharing & permissions**:
    
    - Decide which content is public or private.
        
    - Assign editor groups (who can edit content).
        
4. **Add engagement features** (Premium only):
    
    - Initiatives: big long-term goals, with multiple projects under them.
        
    - Projects: smaller initiatives with tasks, timeline, content.
        
    - Discussion boards, surveys, events, newsletters.
        
5. **Maintain & analyze**:
    
    - Hub includes **analytics** (you can see how users engage) [ArcGIS Docs](https://doc.arcgis.com/en/hub/sites/create-a-site.htm?utm_source=chatgpt.com)
        
    - Update content, publish new maps/apps, track progress through initiatives.
        

---

## 📚 2. StoryMaps

### What are they?

StoryMaps (by Esri) allow you to combine maps with narrative text, images, multimedia (video, audio) into a guided, story-like layout. The goal is to tell a geo-narrative: “Here’s the map, here’s the story, here’s how it evolves.”

You can create:

- Straightforward map + text stories
    
- Side-by-side maps
    
- Swipe or compare maps
    
- Tours (step through locations)
    
- Embedded content (videos, charts, media)
    

They’re used to communicate spatial stories to a broad audience (non-GIS folks), in a visually engaging way.

---

### How to use StoryMaps

1. Sign in to StoryMaps (through your ArcGIS account).
    
2. Create a **new story**: choose a layout (e.g. “Map Tour,” “Sidecar,” “Swipe,” “Multimedia”).
    
3. Add content sections (“slides” or “blocks”):
    
    - Insert a map (existing web map or make new one).
        
    - Add text, images, captions.
        
    - For map sections: you can set view extent, layers, popups.
        
4. Use interactive tools:
    
    - Swipe / compare map layers
        
    - Side-by-side maps
        
    - Animation via “map transitions”
        
5. Publish and embed: once done, you can share a link or embed it in websites or Hub.
    

Tip: Use clean, concise narrative, tie map views to story sections. Use multimedia to engage.

---

## 📈 3. ArcGIS Monitor

### What is it?

ArcGIS Monitor is a monitoring and observability solution for your **ArcGIS Enterprise** deployment. [Esri+3enterprise.arcgis.com+3ArcGIS Docs+3](https://enterprise.arcgis.com/en/monitor/?utm_source=chatgpt.com)

It collects metrics across your GIS infrastructure — servers, services, databases — and provides dashboards, alerts, and reports to help you detect performance issues, usage trends, and system health. [ArcGIS Docs+2Esri+2](https://doc.arcgis.com/en/monitor/latest/get-started/windows/introduction-to-arcgis-monitor.htm?utm_source=chatgpt.com)

Newer versions (2025) allow registering remote ArcGIS Online or third-party ArcGIS Server endpoints, and filtering dashboards by date/time windows. [Esri](https://www.esri.com/arcgis-blog/products/monitor/administration/whats-new-in-arcgis-monitor-2025-0?utm_source=chatgpt.com)

---

### Key capabilities / components

- **Monitor Server**: web interface, data repository. [ArcGIS Docs+1](https://doc.arcgis.com/en/monitor/latest/get-started/windows/introduction-to-arcgis-monitor.htm?utm_source=chatgpt.com)
    
- **Monitor Agent**: runs on machines, collects metrics (CPU, RAM, service response, errors). [ArcGIS Docs](https://doc.arcgis.com/en/monitor/latest/get-started/windows/introduction-to-arcgis-monitor.htm?utm_source=chatgpt.com)
    
- **Registered Components**: you register your GIS components (Portal, ArcGIS Server, DB, machines) so Monitor knows what to track. [Esri+1](https://www.esri.com/arcgis-blog/products/monitor/administration/getting-started-with-arcgis-monitor?utm_source=chatgpt.com)
    
- **Collections / Analysis Views**: group similar components (e.g. all map servers) and build dashboards of metrics. [Esri+1](https://www.esri.com/arcgis-blog/products/monitor/administration/getting-started-with-arcgis-monitor?utm_source=chatgpt.com)
    
- **Alerts / Notifications**: set threshold triggers (e.g. CPU > 90%, service errors) and send email/webhooks. [enterprise.arcgis.com+1](https://enterprise.arcgis.com/en/monitor/?utm_source=chatgpt.com)
    
- **Historical Reporting & Trends**: see how performance has changed over time. [Esri+1](https://www.esri.com/en-us/arcgis/products/arcgis-monitor/overview?utm_source=chatgpt.com)
    

---

### How to use (getting started)

1. **Install Monitor Server and Agent** (Windows or Linux). [ArcGIS Docs+2ArcGIS Docs+2](https://doc.arcgis.com/en/monitor/latest/install/windows/welcome-to-the-arcgis-monitor-windows-installation-guide.htm?utm_source=chatgpt.com)
    
2. Set up a **database repository** for Monitor (dedicated). [ArcGIS Docs+1](https://doc.arcgis.com/en/monitor/latest/get-started/windows/introduction-to-arcgis-monitor.htm?utm_source=chatgpt.com)
    
3. **Register your enterprise GIS components** (Portal, ArcGIS Server sites, DB, etc.). [Esri+1](https://www.esri.com/arcgis-blog/products/monitor/administration/getting-started-with-arcgis-monitor?utm_source=chatgpt.com)
    
4. **Set up alerts / thresholds**: system memory, CPU, response time, service availability. [enterprise.arcgis.com+1](https://enterprise.arcgis.com/en/monitor/?utm_source=chatgpt.com)
    
5. Create **Collections & Analysis Views** to group components and visualize metrics. [Esri+1](https://www.esri.com/arcgis-blog/products/monitor/administration/getting-started-with-arcgis-monitor?utm_source=chatgpt.com)
    
6. Configure **notifications** (email, webhooks) so you’re alerted when metrics exceed thresholds. [Esri+1](https://www.esri.com/arcgis-blog/products/monitor/administration/getting-started-with-arcgis-monitor?utm_source=chatgpt.com)
    
7. Use **reports and dashboards** to share system health insights with managers or IT. [Esri+1](https://www.esri.com/en-us/arcgis/products/arcgis-monitor/overview?utm_source=chatgpt.com)
    
8. **Maintain & tune**: adjust thresholds, add new components, archive old metrics.
    

---

### When to use Monitor vs other tools

- If you have a **multi-machine ArcGIS Enterprise deployment**, Monitor is nearly essential to proactively catch issues.
    
- It complements general IT tools (Nagios, Splunk) by offering GIS-specific insight (service error logs, GIS load, usage).
    
- Use it for SLA compliance, capacity planning, outage analysis, performance tuning.
    

---

## 📊 Summary table

|Product|Purpose / Use|Key Features|When to Use It|
|---|---|---|---|
|**ArcGIS Hub**|Organize & share GIS data, engage stakeholders|Hub sites, catalogs, initiatives, events, surveys|You want to build public or internal GIS portals, track projects, share data|
|**StoryMaps**|Narrative + map storytelling|Map slides, multimedia, guided story flow|You want to present maps in a narrative form to non-technical audiences|
|**ArcGIS Monitor**|Monitor GIS system health & performance|Metrics, alerts, dashboards, historical trends|You run ArcGIS Enterprise and need to manage performance, reliability, usage|